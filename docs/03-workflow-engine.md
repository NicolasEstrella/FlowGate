# FlowGate — Workflow Engine

## O que é o Workflow Engine

O **Workflow Engine** é o coração do FlowGate. É um motor genérico responsável por:

- Instanciar e executar workflows configuráveis
- Controlar o ciclo de vida de cada solicitação
- Orquestrar etapas, dependências e aprovações
- Aplicar regras condicionais via Rule Engine
- Gerenciar transições de estado via State Machine
- Disparar eventos para processamento assíncrono

---

## State Machine — Ciclo de Vida de uma Solicitação

```mermaid
stateDiagram-v2
    [*] --> Rascunho : Criação

    Rascunho --> Submetido : Enviar Solicitação
    Rascunho --> Cancelado : Cancelar

    Submetido --> EmAnalise : Workflow Iniciado
    Submetido --> Cancelado : Cancelar

    EmAnalise --> AguardandoAprovacao : Regras Avaliadas
    EmAnalise --> Rejeitado : Regra Bloqueia

    AguardandoAprovacao --> AprovadoNivel1 : Gerente Aprova
    AguardandoAprovacao --> Rejeitado : Gerente Rejeita
    AguardandoAprovacao --> AjusteNecessario : Solicitar Revisão
    AguardandoAprovacao --> Escalado : SLA Expirado

    AjusteNecessario --> Submetido : Usuário Reenvia
    AjusteNecessario --> Cancelado : Usuário Cancela

    Escalado --> AguardandoAprovacao : Supervisor Assume
    Escalado --> Rejeitado : Supervisor Rejeita

    AprovadoNivel1 --> AprovadoFinanceiro : Financeiro Aprova
    AprovadoNivel1 --> Rejeitado : Financeiro Rejeita
    AprovadoNivel1 --> AjusteNecessario : Solicitar Revisão

    AprovadoFinanceiro --> DocumentoGerado : Documento Criado
    DocumentoGerado --> Concluido : Pagamento Liberado

    Rejeitado --> [*]
    Cancelado --> [*]
    Concluido --> [*]
```

---

## Rule Engine — Regras Condicionais

O **Rule Engine** avalia regras dinâmicas para determinar:

- Quais etapas são necessárias
- Quem deve aprovar
- Se exige documentação adicional
- Se pode seguir em paralelo

### Exemplo de Regras (pseudocódigo SDD)

```
DADO uma solicitação de reembolso com valor > 5.000
QUANDO for submetida
ENTÃO exigir aprovação do Financeiro

DADO uma solicitação de reembolso com valor > 10.000
QUANDO for submetida
ENTÃO exigir aprovação do Diretor

DADO uma solicitação de contrato com valor > 50.000
QUANDO for aprovada pelo Financeiro
ENTÃO encaminhar para o Jurídico

DADO uma solicitação parada na etapa de aprovação por mais de 48h
QUANDO o SLA expirar
ENTÃO escalar para o supervisor do aprovador
```

### Fluxo do Rule Engine

```mermaid
flowchart TD
    Start([Solicitação Recebida]) --> Eval{Avaliar Regras}

    Eval --> R1{Valor > 10k?}
    R1 -- Sim --> Step1[+ Etapa: Aprovação Diretoria]
    R1 -- Não --> R2{Valor > 5k?}

    R2 -- Sim --> Step2[+ Etapa: Aprovação Financeiro]
    R2 -- Não --> R3{Tipo = Contrato?}

    R3 -- Sim --> Step3[+ Etapa: Revisão Jurídica]
    R3 -- Não --> Assemble

    Step1 --> Assemble
    Step2 --> Assemble
    Step3 --> Assemble

    Assemble[Montar Fluxo de Etapas] --> Execute[Executar Workflow]
```

---

## Orquestração de Etapas

O engine suporta diferentes modos de execução:

### Sequencial

```mermaid
graph LR
    A[Etapa 1\nGerente] --> B[Etapa 2\nFinanceiro] --> C[Etapa 3\nDiretor] --> D[Concluído]
```

### Paralelo

```mermaid
graph TD
    Start([Início]) --> A[Aprovação Financeiro]
    Start --> B[Revisão Jurídica]
    A --> Merge{Ambos Aprovados?}
    B --> Merge
    Merge -- Sim --> Done([Concluído])
    Merge -- Não --> Rejected([Rejeitado])
```

### Com Condição

```mermaid
graph TD
    Start([Início]) --> Cond{Valor > 5k?}
    Cond -- Sim --> A[Etapa Extra:\nFinanceiro]
    Cond -- Não --> B[Etapa Padrão:\nGerente]
    A --> Done([Concluído])
    B --> Done
```

---

## SLA e Escalonamento

```mermaid
sequenceDiagram
    participant WE as Workflow Engine
    participant SLA as SLA Monitor
    participant MQ as RabbitMQ
    participant EW as Escalation Worker
    participant SUP as Supervisor

    WE->>SLA: Registrar deadline da etapa (48h)
    Note over SLA: Monitoramento contínuo

    SLA->>SLA: Verificar etapas vencidas (job periódico)
    SLA->>MQ: EscalationTriggeredEvent
    MQ->>EW: Processar escalonamento
    EW->>EW: Buscar supervisor do aprovador
    EW->>WE: Reatribuir etapa
    EW->>MQ: NotifyEscalationEvent
    MQ-->>SUP: Notificação: etapa escalada para você
```

---

## Spec-Driven Development (SDD)

O FlowGate usa **SDD com OpenSpec** — toda regra de negócio nasce como uma especificação comportamental antes de ser implementada.

### Formato de Especificação

```gherkin
Feature: Aprovação de Reembolso Corporativo

  Scenario: Reembolso abaixo de 5.000 reais
    Given um funcionário submete reembolso de R$ 3.000
    When o workflow é iniciado
    Then apenas o gerente direto deve aprovar
    And o prazo de aprovação é de 48 horas

  Scenario: Reembolso acima de 5.000 reais
    Given um funcionário submete reembolso de R$ 7.000
    When o workflow é iniciado
    Then o gerente direto deve aprovar primeiro
    And o financeiro deve aprovar em seguida
    And o prazo total é de 72 horas

  Scenario: Reembolso acima de 10.000 reais
    Given um funcionário submete reembolso de R$ 12.000
    When o workflow é iniciado
    Then o gerente direto deve aprovar
    And o financeiro deve aprovar
    And o diretor deve aprovar por último
    And qualquer rejeição encerra o fluxo

  Scenario: SLA expirado sem aprovação
    Given uma etapa está pendente há mais de 48 horas
    When o SLA monitor verifica as etapas
    Then a etapa é escalada para o supervisor do aprovador
    And o aprovador original é notificado
    And o supervisor recebe notificação de urgência
```

---

## Processamento Assíncrono

```mermaid
graph LR
    subgraph Sync["Síncrono (API)"]
        API[Controller] --> WE[Workflow Engine]
        WE --> DB[(PostgreSQL)]
    end

    subgraph Async["Assíncrono (Workers)"]
        MQ[RabbitMQ] --> WN[Notification\nWorker]
        MQ --> WD[Document\nWorker]
        MQ --> WES[Escalation\nWorker]
    end

    WE -->|Publica Evento| MQ
    WN -->|Email/Push| EXT[Serviços Externos]
    WD -->|PDF/DOCX| MN[MinIO]
    WES -->|Reatribui| WE
```

### Eventos do Sistema

| Evento | Produzido por | Consumido por |
|--------|--------------|--------------|
| `RequestCreatedEvent` | Workflow Engine | Notification Worker |
| `StepAssignedEvent` | Workflow Engine | Notification Worker |
| `ApprovedEvent` | Workflow Engine | Notification + Document Worker |
| `RejectedEvent` | Workflow Engine | Notification Worker + Audit |
| `EscalationTriggeredEvent` | SLA Monitor | Escalation Worker |
| `RequestCompletedEvent` | Workflow Engine | Document + Notification Worker |
| `DocumentGeneratedEvent` | Document Worker | Notification Worker |

---

## Navegação

- [Overview](01-overview.md)
- [Arquitetura](02-architecture.md)
- [Funcionalidades](04-features.md)
- [Casos de Uso](05-use-cases.md)
