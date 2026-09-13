# Flow.CLI

Console entry point of Flow. `Program.cs` is still "Hello, World!".

The only design signal so far is the dependency set: `Microsoft.Agents.AI` and `Microsoft.Agents.AI.OpenAI` (Microsoft Agent Framework), so the intended direction is an AI-agent CLI using OpenAI-compatible chat clients.

## Commands

```bash
dotnet run --project src/Flow.CLI
```

## Conventions

- `ImplicitUsings` and `Nullable` are enabled; keep new code nullable-clean.
