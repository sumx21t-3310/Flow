# Flow.CLI

Console entry point of Flow. `Program.cs` is still "Hello, World!".

Dependencies: `Microsoft.Agents.AI` and `Microsoft.Agents.AI.OpenAI` (Microsoft Agent Framework). Run with:

```bash
dotnet run --project src/Flow.CLI
```

## Scope

- Owns: command-line argument handling, console display, wiring of the agent runtime.
- During Phase 1 this project also hosts the `Flow.Core.*` namespaces (judges, stages, plan file) because no `Flow.Core` csproj exists yet. Keep them in a `Core/` directory so that Phase 2 can move them by file relocation. Namespace and physical directory must match.
- Does not own: writing-stage logic, judge logic, plan-file schema. Those live under `Flow.Core.*` even while they sit in this csproj.

## Review Points

- Did any `Flow.Core.*` type gain a dependency on `Flow.CLI.*`, `System.Console`, or argument parsing? That is the boundary Phase 2 relies on. (Temporary: moves to Acceptance Conditions once the reference test exists in Phase 1b.)
- Did the pass/fail decision of a loop come from a judge, or from the LLM evaluating its own output? Only the judge decides.

## Acceptance Conditions

Contracts that must stay true after every change. Each becomes a test once the test project exists (Phase 1b); until then, review enforces them.

- `Flow.Core.*` types reference no `Flow.CLI.*` type and no `System.Console` member.
- Every judge exposes a deterministic pass/fail result for a given input; no judge calls an LLM to decide pass/fail.
