# FlowGate — Copilot Instructions

## Contexto do Projeto

FlowGate é uma **plataforma de automação e aprovação empresarial** baseada em workflows dinâmicos e configuráveis. Antes de qualquer implementação, leia a documentação em `docs/`:

| Arquivo | Conteúdo |
|---|---|
| `docs/01-overview.md` | O que é o FlowGate, problema que resolve, diferenciais |
| `docs/02-architecture.md` | Stack, componentes, arquitetura de dados, RBAC |
| `docs/03-workflow-engine.md` | State machine, Rule Engine, SLA, SDD com OpenSpec |
| `docs/04-features.md` | Funcionalidades do sistema |
| `docs/05-use-cases.md` | Casos de uso reais |

**Sempre leia os arquivos relevantes de `docs/` antes de propor ou implementar qualquer feature.**

---

## Stack

- **Backend:** C#, ASP.NET Core (.NET 8), Entity Framework Core 8, MassTransit, PostgreSQL
- **Frontend:** Angular 19+, TypeScript, SCSS
- **Mensageria:** RabbitMQ via MassTransit
- **Infra:** Docker Compose (`FlowGate.Infra`)

---

## Backend — Test-Driven Development (TDD)

Todo desenvolvimento no backend (`FlowGate.Core`) deve seguir o ciclo TDD:

### Ciclo Obrigatório: Red → Green → Refactor

1. **Red:** escreva o teste que descreve o comportamento esperado — ele deve falhar
2. **Green:** implemente o mínimo de código para o teste passar
3. **Refactor:** melhore o código sem quebrar os testes

### Ferramentas de Teste

- **xUnit** — framework de testes
- **Moq** — mocking de dependências
- **FluentAssertions** — assertions legíveis
- **Microsoft.AspNetCore.Mvc.Testing** — testes de integração de API

### Organização dos Testes

```
FlowGate.Core.Tests/
  Unit/
    Domain/          # Testes de entidades e regras de domínio
    Services/        # Testes de serviços de aplicação
    Engine/          # Testes do Workflow Engine e Rule Engine
  Integration/
    Api/             # Testes de endpoints HTTP
    Persistence/     # Testes de repositórios com banco real
```

### Convenções de Nomenclatura

Use o padrão `MetodoTestado_Cenario_ResultadoEsperado`:

```csharp
// ✅ Correto
public async Task StartWorkflow_WithValidRequest_ShouldCreateInstance()
public async Task EvaluateRules_WhenAmountExceeds5000_ShouldAddFinanceStep()
public async Task TriggerEscalation_WhenSlaExpired_ShouldNotifySuperviso()

// ❌ Errado
public async Task TestWorkflow()
public async Task Test1()
```

### Exemplo de Estrutura de Teste

```csharp
public class WorkflowEngineTests
{
    private readonly Mock<IWorkflowRepository> _repositoryMock;
    private readonly WorkflowEngine _sut; // System Under Test

    public WorkflowEngineTests()
    {
        _repositoryMock = new Mock<IWorkflowRepository>();
        _sut = new WorkflowEngine(_repositoryMock.Object);
    }

    [Fact]
    public async Task StartWorkflow_WithValidRequest_ShouldCreateInstance()
    {
        // Arrange
        var request = new StartWorkflowRequest { ... };

        // Act
        var result = await _sut.StartAsync(request);

        // Assert
        result.Should().NotBeNull();
        result.Status.Should().Be(WorkflowStatus.InProgress);
        _repositoryMock.Verify(r => r.SaveAsync(It.IsAny<WorkflowInstance>()), Times.Once);
    }
}
```

### Regras Gerais de TDD

- **Nunca** implemente lógica de negócio sem escrever o teste antes
- Testes de **domínio e serviços** devem ser unitários (sem banco, sem HTTP)
- **State machine** e **Rule Engine** têm cobertura obrigatória de testes unitários
- Ao adicionar um novo endpoint, escreva testes de integração para os casos feliz e de erro
- Mantenha a cobertura de testes acima de **80%** no projeto `FlowGate.Core`

---

## Metodologia: Spec-Driven Development (SDD)

O FlowGate usa SDD — toda regra de negócio nasce como especificação comportamental (Gherkin/Given-When-Then) antes de ser implementada. Os specs ficam em `openspec/`.

Quando implementar uma feature:
1. Verifique se existe uma spec em `openspec/` para ela
2. Os cenários da spec devem virar testes xUnit (TDD)
3. Implemente o código para os testes passarem

---

## Padrões Gerais

- Respostas e commits em **inglês**
- Comentários de código em **inglês**
- Conversas e explicações em **português**
- Siga os padrões de arquitetura definidos em `docs/02-architecture.md` (Clean Architecture: API → Domain → Infra)
- Eventos do sistema seguem o padrão `NomeDoEventoEvent` (ex: `RequestCreatedEvent`)
