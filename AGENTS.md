# Flow

## Overview

Flow aims to be a writing agent: it runs the whole pipeline from planning to proofreading as an agent loop, the way a coding agent iterates on code. The reference experience is the report feature of NotebookLM (source-grounded, multi-step generation). It is a .NET 10 solution (`Flow.slnx`, XML solution format) at the skeleton stage. Projects live under `src/`. Design decisions live in `docs/design.md`; read it before changing the pipeline or the acceptance-check layers.

Current projects:

- `src/Flow.CLI` — console app (see `src/Flow.CLI/AGENTS.md`)

## Commands

Run from the repository root. Paths are given in `C:/...` form because the Bash tool is Git Bash.

```bash
dotnet build Flow.slnx
```

There is no test project yet. When one is added, register it in `Flow.slnx` under the `/src/` folder and run it with `dotnet test Flow.slnx` (single test: `dotnet test --filter "FullyQualifiedName~<TestName>"`).

## Conventions

- Agent instructions are layered 1:1 with projects. This file covers the solution; each `.csproj` directory has its own `AGENTS.md` (the single source) plus a `CLAUDE.md` stub containing only `@AGENTS.md`. Claude Code loads a subdirectory `CLAUDE.md` lazily when it touches files there, so project-specific detail belongs in the project's `AGENTS.md`, not here.
- Adding a project: place it under `src/`, add a `<Project Path="src/<Name>/<Name>.csproj" />` entry inside the `/src/` folder in `Flow.slnx`, and create the `AGENTS.md` / `CLAUDE.md` pair (copy the stub from `src/Flow.CLI/CLAUDE.md`).
- `bin/` and `obj/` are gitignored. `.idea/` (Rider) is untracked and should stay out of commits.

## Workflow: issue-driven

- Development is issue-driven. When a design discussion reaches a conclusion, file it as a GitHub Issue (`gh issue create`) before writing code. The issue holds the decision and its acceptance criteria; `docs/design.md` keeps the durable picture and links to issues where relevant.
- Work on a branch per issue and reference the issue number in the PR. `main` is not pushed to directly.
- Open questions listed under "未決定" in `docs/design.md` become issues when they are picked up, one issue per question.
