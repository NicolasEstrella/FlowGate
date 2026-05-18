# FlowGate — Casos de Uso

## Caso 1: Reembolso Corporativo

### Contexto

Um funcionário realizou uma despesa de R$ 6.000 em uma viagem corporativa e precisa solicitar reembolso.

### Fluxo Completo

```mermaid
sequenceDiagram
    actor F as Funcionário
    participant API as FlowGate API
    participant WE as Workflow Engine
    participant RE as Rule Engine
    participant DB as PostgreSQL
    participant MQ as RabbitMQ
    participant NS as Notification Worker
    actor G as Gerente
    actor FIN as Financeiro
    participant DS as Document Worker
    participant FS as Volume Local

    F->>API: POST /api/requests\n{tipo: reembolso, valor: 6000}
    API->>WE: Iniciar workflow
    WE->>RE: Avaliar regras para R$ 6.000
    RE->>WE: [Etapa 1: Gerente] [Etapa 2: Financeiro]
    WE->>DB: Persistir instância (estado: AguardandoAprovacao)
    WE->>MQ: RequestCreatedEvent
    MQ->>NS: Enviar email para Gerente
    NS-->>G: 📧 "Solicitação aguardando sua aprovação"

    G->>API: PUT /api/approvals/:id\n{acao: aprovado, comentario: "OK"}
    API->>WE: Processar aprovação do Gerente
    WE->>DB: Atualizar estado + auditoria
    WE->>MQ: ApprovedByManagerEvent
    MQ->>NS: Notificar Financeiro
    NS-->>FIN: 📧 "Reembolso R$ 6.000 aguarda aprovação financeira"

    FIN->>API: PUT /api/approvals/:id\n{acao: aprovado}
    API->>WE: Processar aprovação Financeiro
    WE->>DB: Estado → Concluido
    WE->>MQ: RequestCompletedEvent
    MQ->>DS: Gerar comprovante de aprovação
    DS->>DS: Montar PDF com dados + QRCode
    DS->>FS: Salvar documento
    MQ->>NS: Notificar Funcionário
    NS-->>F: 📧 "Reembolso aprovado! Pagamento em até 3 dias úteis."
```

### Regras Aplicadas

| Regra | Valor | Resultado |
|-------|-------|-----------|
| `valor > 5.000` | R$ 6.000 ✅ | Exige aprovação do Financeiro |
| `valor > 10.000` | R$ 6.000 ❌ | Não exige aprovação do Diretor |

### Estados percorridos

```mermaid
graph LR
    A([Rascunho]) --> B([Submetido]) --> C([EmAnalise]) --> D([AguardandoAprovacao])
    D --> E([AprovadoNivel1]) --> F([AprovadoFinanceiro]) --> G([Concluido])
```

---

## Caso 2: Reembolso com SLA Expirado

### Contexto

O mesmo reembolso, mas o Gerente não responde em 48h.

```mermaid
sequenceDiagram
    actor F as Funcionário
    participant WE as Workflow Engine
    participant SLA as SLA Monitor
    participant MQ as RabbitMQ
    participant EW as Escalation Worker
    actor G as Gerente
    actor S as Supervisor do Gerente
    participant NS as Notification Worker

    WE->>SLA: Registrar deadline: 48h para Gerente
    Note over SLA: 47h depois...
    SLA->>MQ: NotifyDeadlineApproachingEvent
    MQ->>NS: Aviso de prazo próximo
    NS-->>G: ⚠️ "Você tem 1h para aprovar o reembolso de R$ 6.000"

    Note over SLA: 48h expirado - sem resposta
    SLA->>MQ: EscalationTriggeredEvent
    MQ->>EW: Processar escalonamento
    EW->>EW: Buscar supervisor do Gerente
    EW->>WE: Reatribuir etapa para Supervisor
    EW->>MQ: NotifyEscalationEvent
    MQ->>NS: Notificar ambos
    NS-->>G: 🚨 "Sua aprovação foi escalada para seu supervisor"
    NS-->>S: 🚨 "Você recebeu uma aprovação escalada por prazo expirado"
```

---

## Caso 3: Aprovação de Contrato (Paralela)

### Contexto

Um contrato de R$ 80.000 precisa de aprovação simultânea do Financeiro e do Jurídico.

```mermaid
graph TD
    Start([Contrato Submetido\nR$ 80.000]) --> RE{Rule Engine}

    RE --> R1[Regra: valor > 50k\n→ Jurídico obrigatório]
    RE --> R2[Regra: tipo = contrato\n→ Financeiro obrigatório]

    R1 --> PAR{Execução Paralela}
    R2 --> PAR

    PAR --> AP1[Aprovação\nFinanceiro]
    PAR --> AP2[Revisão\nJurídico]

    AP1 --> MERGE{Ambos concluídos?}
    AP2 --> MERGE

    MERGE -- Sim, ambos aprovaram --> DIR[Aprovação\nDiretor]
    MERGE -- Um rejeitou --> REJ([Rejeitado])

    DIR --> DONE([Contrato Aprovado])
```

### Estados percorridos

```mermaid
stateDiagram-v2
    [*] --> Submetido
    Submetido --> EmAnalise
    EmAnalise --> AprovacaoParalela : Valor > 50k + Contrato
    AprovacaoParalela --> AguardandoDiretor : Financeiro ✅ + Jurídico ✅
    AprovacaoParalela --> Rejeitado : Qualquer rejeição
    AguardandoDiretor --> Concluido : Diretor aprova
    AguardandoDiretor --> Rejeitado : Diretor rejeita
    Rejeitado --> [*]
    Concluido --> [*]
```

---

## Caso 4: Solicitação com Pedido de Ajuste

### Contexto

Funcionário submete reembolso com comprovante incorreto. Gerente pede revisão.

```mermaid
sequenceDiagram
    actor F as Funcionário
    participant WE as Workflow Engine
    actor G as Gerente
    participant NS as Notification Worker

    F->>WE: Submete reembolso com comprovante incorreto
    WE-->>G: Notifica para aprovação

    G->>WE: PUT /approvals/:id {acao: ajuste, motivo: "Comprovante ilegível"}
    WE->>WE: Estado → AjusteNecessario
    WE-->>NS: StepAdjustmentRequestedEvent
    NS-->>F: 📧 "O Gerente solicitou ajuste: Comprovante ilegível"

    F->>WE: PUT /requests/:id {anexo: novo_comprovante.pdf}
    WE->>WE: Estado → Submetido (reinicia fluxo)
    WE-->>G: Notifica nova submissão

    G->>WE: PUT /approvals/:id {acao: aprovado}
    WE->>WE: Continua fluxo normalmente
```

---

## Caso 5: Onboarding de Funcionário

### Contexto

Novo funcionário contratado precisa passar por múltiplas etapas de RH, TI e Gestão.

```mermaid
graph TD
    Start([Funcionário Contratado]) --> S1

    subgraph RH["RH — Sequencial"]
        S1[Coleta de Documentos] --> S2[Validação Cadastral] --> S3[Assinatura de Contrato]
    end

    S3 --> S4

    subgraph TI_RH["TI + Benefícios — Paralelo"]
        S4[Criação de Conta\nno Sistema]
        S5[Cadastro de\nBenefícios]
    end

    S4 --> MERGE{Ambos OK?}
    S5 --> MERGE

    MERGE --> S6[Integração com\nGestor Direto]
    S6 --> S7[Treinamentos\nObrigatórios]
    S7 --> Done([Onboarding Concluído])
```

### Especificação SDD

```gherkin
Feature: Onboarding de Novo Funcionário

  Scenario: Onboarding padrão CLT
    Given um funcionário CLT é cadastrado no sistema
    When o onboarding é iniciado
    Then o RH deve coletar documentos em até 24 horas
    And o contrato deve ser assinado em até 48 horas
    And TI e Benefícios são ativados em paralelo
    And o gestor deve fazer a integração em até 5 dias úteis

  Scenario: Documentação incompleta
    Given o funcionário enviou documentos incompletos
    When o RH valida o cadastro
    Then o status muda para AjusteNecessario
    And o funcionário é notificado com a lista de pendências
    And o prazo é reiniciado após o reenvio
```

---

## Resumo dos Casos

| Caso | Tipo | Complexidade |
|------|------|-------------|
| Reembolso simples | Sequencial com regras de valor | ⭐⭐ |
| Reembolso com SLA | Sequencial + escalonamento | ⭐⭐⭐ |
| Contrato | Paralelo + múltiplos aprovadores | ⭐⭐⭐⭐ |
| Ajuste/Revisão | Sequencial com loop de correção | ⭐⭐⭐ |
| Onboarding | Misto (sequencial + paralelo) | ⭐⭐⭐⭐⭐ |

---

## Navegação

- [Overview](01-overview.md)
- [Arquitetura](02-architecture.md)
- [Workflow Engine](03-workflow-engine.md)
- [Funcionalidades](04-features.md)
