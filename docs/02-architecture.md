# FlowGate — Arquitetura Técnica

## Stack

| Camada | Tecnologias |
|--------|------------|
| **Backend** | C#, ASP.NET Core, Entity Framework Core, MassTransit |
| **Frontend** | Angular, TypeScript, SCSS |
| **Mensageria** | RabbitMQ |
| **Banco principal** | PostgreSQL |
| **Cache** | Redis |
| **Armazenamento** | Volume Local (filesystem montado via Docker) |
| **Gateway** | NGINX |
| **Infra** | Docker, Docker Compose |

---

## Visão Geral do Sistema

```mermaid
graph TB
    subgraph Frontend["Frontend — Angular"]
        UI[Angular + TypeScript]
        WF[Portal Web\nFluxos e Aprovações]
        UI --- WF
    end

    subgraph Gateway["API Gateway"]
        NGINX[NGINX\nProxy + Load Balancer]
    end

    subgraph Backend["FlowGate.Core — ASP.NET Core"]
        API[REST API\nControllers]
        WE[Workflow Engine]
        RE[Rule Engine]
        SM[State Machine]
        API --> WE
        WE --> RE
        WE --> SM
    end

    subgraph Data["Persistência"]
        PG[(PostgreSQL\nDados principais)]
        RD[(Redis\nCache + Sessões)]
    end

    subgraph Messaging["Mensageria — RabbitMQ"]
        MQ[RabbitMQ\nBroker]
        MT[MassTransit\nAbstração]
    end

    subgraph Workers["Workers Assíncronos"]
        WN[Notification Worker\nEmail + Push]
        WD[Document Worker\nPDF + DOCX]
        WE2[Escalation Worker\nSLA + Timeout]
    end

    subgraph Storage["Armazenamento"]
        FS[Volume Local\nDocumentos + Anexos]
    end

    Frontend --> Gateway
    Gateway --> Backend
    Backend --> Data
    Backend --> Messaging
    Messaging --> Workers
    Workers --> Storage
    Workers --> Frontend
```

---

## Fluxo de Dados

```mermaid
sequenceDiagram
    participant U as Usuário
    participant API as FlowGate.Core
    participant WE as Workflow Engine
    participant DB as PostgreSQL
    participant MQ as RabbitMQ
    participant NS as Notification Worker
    participant DS as Document Worker

    U->>API: POST /api/requests
    API->>WE: Iniciar Workflow
    WE->>DB: Persistir estado inicial
    WE->>MQ: Publicar RequestCreatedEvent
    MQ->>NS: Notificar aprovadores
    NS-->>U: Email / Push enviado

    U->>API: PUT /api/approvals/:id (Aprovação)
    API->>WE: Processar transição de estado
    WE->>DB: Atualizar estado + auditoria
    WE->>MQ: Publicar ApprovedEvent
    MQ->>DS: Gerar documento de aprovação
    DS->>MQ: DocumentGeneratedEvent
    MQ->>NS: Notificar próximo aprovador
```

---

## Componentes do Backend

```mermaid
graph LR
    subgraph API["API Layer"]
        C[Controllers]
        MW[Middlewares\nAuth + Logging]
        V[Validators\nFluentValidation]
    end

    subgraph Core["Domain Layer"]
        WE[Workflow Engine]
        RE[Rule Engine]
        SM[State Machine]
        SLA[SLA Monitor]
    end

    subgraph Infra["Infrastructure Layer"]
        EF[EF Core\nRepositories]
        MT[MassTransit\nPublishers]
        RC[Redis Cache]
        FS[FileSystem\nLocal Volume]
    end

    subgraph Events["Event Handlers"]
        EH1[RequestCreatedHandler]
        EH2[ApprovedHandler]
        EH3[RejectedHandler]
        EH4[EscalationHandler]
        EH5[DocumentHandler]
    end

    API --> Core
    Core --> Infra
    Infra --> Events
```

---

## Estrutura de Eventos (Event-Driven)

```mermaid
graph TD
    subgraph Eventos["Eventos do Sistema"]
        E1[RequestCreatedEvent]
        E2[StepCompletedEvent]
        E3[ApprovedByManagerEvent]
        E4[ApprovedByFinanceEvent]
        E5[RejectedEvent]
        E6[EscalationTriggeredEvent]
        E7[RequestCompletedEvent]
        E8[DocumentGeneratedEvent]
    end

    subgraph Handlers["Event Handlers"]
        H1[SendNotificationHandler]
        H2[GenerateDocumentHandler]
        H3[UpdateAuditHandler]
        H4[EscalateApprovalHandler]
        H5[ReleasePaymentHandler]
    end

    E1 --> H1
    E1 --> H3
    E2 --> H1
    E3 --> H1
    E3 --> H2
    E4 --> H1
    E4 --> H5
    E5 --> H1
    E5 --> H3
    E6 --> H4
    E6 --> H1
    E7 --> H2
    E7 --> H1
    E8 --> H1
```

---

## Arquitetura de Dados

```mermaid
erDiagram
    Workflow {
        uuid Id
        string Name
        string Description
        string Status
        json StepsDefinition
        datetime CreatedAt
    }

    WorkflowInstance {
        uuid Id
        uuid WorkflowId
        string CurrentStep
        string Status
        uuid CreatedBy
        datetime CreatedAt
        datetime UpdatedAt
    }

    ApprovalStep {
        uuid Id
        uuid InstanceId
        string StepName
        string Status
        uuid AssignedTo
        datetime Deadline
        datetime CompletedAt
    }

    AuditLog {
        uuid Id
        uuid InstanceId
        uuid UserId
        string Action
        string PreviousState
        string NewState
        string IpAddress
        datetime Timestamp
    }

    User {
        uuid Id
        string Name
        string Email
        string Role
    }

    Workflow ||--o{ WorkflowInstance : "instancia"
    WorkflowInstance ||--o{ ApprovalStep : "possui"
    WorkflowInstance ||--o{ AuditLog : "registra"
    User ||--o{ ApprovalStep : "aprova"
    User ||--o{ AuditLog : "executa"
```

---

## Permissões (RBAC)

```mermaid
graph TD
    subgraph Roles["Papéis"]
        AD[Admin]
        AP[Aprovador]
        FN[Financeiro]
        JR[Jurídico]
        US[Usuário Comum]
    end

    subgraph Permissions["Permissões"]
        P1[Criar Workflows]
        P2[Aprovar Solicitações]
        P3[Aprovar Financeiro]
        P4[Aprovar Jurídico]
        P5[Criar Solicitações]
        P6[Ver Próprias Solicitações]
        P7[Gerenciar Usuários]
        P8[Ver Auditoria]
    end

    AD --> P1
    AD --> P7
    AD --> P8
    AD --> P2
    AP --> P2
    AP --> P6
    FN --> P3
    FN --> P6
    JR --> P4
    JR --> P6
    US --> P5
    US --> P6
```

---

## Navegação

- [Overview](01-overview.md)
- [Workflow Engine](03-workflow-engine.md)
- [Funcionalidades](04-features.md)
- [Casos de Uso](05-use-cases.md)
