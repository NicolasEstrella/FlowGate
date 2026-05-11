# FlowGate

> Plataforma de automação e aprovação empresarial baseada em workflows dinâmicos.

FlowGate é uma engine de workflows empresariais desenvolvida para automatizar processos internos como aprovações financeiras, reembolsos, contratos e onboarding. A plataforma permite criar fluxos dinâmicos com regras condicionais, aprovações paralelas, SLA, escalonamento automático e processamento assíncrono através de filas.

## Repositórios

| Módulo | Descrição | Repositório |
|--------|-----------|-------------|
| **FlowGate.Core** | Backend .NET 8 Web API + PostgreSQL | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Core) |
| **FlowGate.Portal** | Frontend Angular + TypeScript | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Portal) |
| **FlowGate.Infra** | Docker Compose (ambiente local completo) | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Infra) |

## Stack

| Camada | Tecnologias |
|--------|------------|
| **Backend** | .NET 8, ASP.NET Core, EF Core, MassTransit, RabbitMQ |
| **Frontend** | Angular, TypeScript, SCSS |
| **Banco** | PostgreSQL, Redis |
| **Infra** | Docker, Docker Compose, NGINX, MinIO |

## Arquitetura (visão geral)

```
Frontend (Angular)
     ↓
API Gateway (NGINX)
     ↓
FlowGate.Core (ASP.NET Core)
     ↓
Workflow Engine + Rule Engine
     ↓
RabbitMQ (MassTransit)
     ↓
Workers: Notificações | Documentos | Escalonamento
```

## Documentação

| Documento | Descrição |
|-----------|-----------|
| [Overview](docs/01-overview.md) | O que é o FlowGate, pitch e problema resolvido |
| [Arquitetura](docs/02-architecture.md) | Arquitetura técnica e diagramas do sistema |
| [Workflow Engine](docs/03-workflow-engine.md) | Motor de workflows, state machine e rule engine |
| [Funcionalidades](docs/04-features.md) | Todas as features da plataforma |
| [Casos de Uso](docs/05-use-cases.md) | Exemplos práticos com fluxos detalhados |

## Ambiente local

```bash
# Clonar com todos os submodules
git clone --recurse-submodules https://github.com/NicolasEstrella/FlowGate.git

# Subir ambiente completo
cd FlowGate.Infra
docker compose up -d
```

| Serviço | URL |
|---------|-----|
| Frontend | http://localhost:4200 |
| Backend API | http://localhost:8080 |
| PostgreSQL | localhost:5432 |

## Atualizar submodules

```bash
git submodule update --remote --merge
git add .
git commit -m "chore: update submodules"
git push
```

---

> Documentação completa disponível no [Notion do projeto](https://notion.so).
