# FlowGate

Plataforma FlowGate — repositorio raiz com todos os modulos do projeto.

## Repositorios

| Modulo | Descricao | Repositorio |
|--------|-----------|-------------|
| **FlowGate.Core** | Backend .NET 8 Web API + PostgreSQL | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Core) |
| **FlowGate.Portal** | Frontend Angular + nginx | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Portal) |
| **FlowGate.Infra** | Docker Compose (ambiente local) | [Ver repo](https://github.com/NicolasEstrella/FlowGate.Infra) |

## Stack

- **Backend**: .NET 8 Web API, Entity Framework Core, PostgreSQL
- **Frontend**: Angular 21, TypeScript, SCSS
- **Infra**: Docker Compose, nginx, PostgreSQL 16

## Clonando o projeto completo

```bash
# Clona o repositorio raiz com todos os submodulos
git clone --recurse-submodules https://github.com/NicolasEstrella/FlowGate.git
```

Se ja clonou sem `--recurse-submodules`:
```bash
git submodule update --init --recursive
```

## Ambiente local

```bash
cd FlowGate.Infra
docker compose up -d
```

| Servico     | URL                   |
|-------------|-----------------------|
| Frontend    | http://localhost:4200 |
| Backend API | http://localhost:8080 |
| PostgreSQL  | localhost:5432        |

## Atualizando submodulos

Para atualizar todos os submodulos para o commit mais recente:
```bash
git submodule update --remote --merge
git add .
git commit -m "chore: update submodules"
git push
```

## Documentacao

Consulte a documentacao completa no Notion do projeto.
