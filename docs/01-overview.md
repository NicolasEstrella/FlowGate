# FlowGate — Overview

## O que é

FlowGate é uma **plataforma de automação e aprovação empresarial** baseada em workflows dinâmicos e configuráveis.

O sistema permite criar fluxos para processos corporativos como:

- Aprovação de compras e reembolsos
- Gestão de contratos
- Onboarding de funcionários e fornecedores
- Solicitação de férias e benefícios
- Validação documental
- Cadastro de fornecedores

Tudo isso através de um **motor de workflow** capaz de:

- Executar regras condicionais
- Controlar estados via state machine
- Orquestrar etapas sequenciais e paralelas
- Processar filas assíncronas com retries
- Registrar auditoria completa
- Escalar aprovações automaticamente por SLA

---

## Problema que Resolve

Empresas normalmente possuem processos:

| Problema | Impacto |
|----------|---------|
| Manuais | Lentidão e erros humanos |
| Descentralizados | Perda de rastreabilidade |
| Via e-mail e planilha | Sem histórico, sem auditoria |
| Dependentes de pessoas | Gargalos e escalonamento manual |
| Difíceis de auditar | Risco de compliance |

FlowGate **centraliza** esses processos em workflows automatizados, rastreáveis e auditáveis.

---

## Pitch

### Pitch Curto

> FlowGate é uma plataforma de automação corporativa baseada em workflows configuráveis, permitindo que empresas criem fluxos de aprovação e processamento de forma dinâmica, escalável e auditável.

### Pitch Médio

> FlowGate é uma engine de workflows empresariais desenvolvida para automatizar processos internos como aprovações financeiras, reembolsos, contratos e onboarding.
>
> A plataforma permite criar fluxos dinâmicos com regras condicionais, aprovações paralelas, SLA, escalonamento automático e processamento assíncrono através de filas.
>
> O foco técnico do projeto é arquitetura distribuída, orquestração de processos, state machines, event-driven architecture e Spec-Driven Development (SDD).

---

## Diferenciais

FlowGate vai além de um CRUD tradicional:

| Diferencial | Descrição |
|-------------|-----------|
| **Workflow Engine** | Motor genérico para qualquer processo corporativo |
| **Rule Engine** | Regras condicionais dinâmicas e configuráveis |
| **State Machine** | Controle preciso de estados e transições |
| **Event-Driven** | Ações assíncronas disparadas por eventos |
| **SDD** | Especificações comportamentais guiam o desenvolvimento |
| **Auditoria** | Rastreabilidade completa de cada ação |
| **SLA + Escalonamento** | Prazos e escalonamento automático |

Isso aproxima o sistema de plataformas reais como:

- [Camunda](https://camunda.com/) — workflow engine enterprise
- [Pipefy](https://www.pipefy.com/) — gestão de processos no-code
- [Temporal](https://temporal.io/) — orquestração de workflows distribuídos
- [Jira Workflows](https://www.atlassian.com/software/jira) — fluxos de trabalho
- [SAP Approval Flow](https://www.sap.com/) — aprovações enterprise

---

## Objetivo do Projeto

O objetivo principal é estudar e aplicar na prática:

- Arquitetura enterprise e sistemas distribuídos
- Motores de workflow e orquestração de processos
- Filas assíncronas com RabbitMQ e MassTransit
- State machines e transições de estado
- Regras dinâmicas e rule engines
- Event-Driven Architecture
- **Spec-Driven Development (SDD) com OpenSpec**

Através da construção de uma plataforma realista, escalável e próxima de sistemas de produção reais.

---

## Navegação

- [Arquitetura Técnica](02-architecture.md)
- [Workflow Engine](03-workflow-engine.md)
- [Funcionalidades](04-features.md)
- [Casos de Uso](05-use-cases.md)
