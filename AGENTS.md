# Flow

This file is the single source of project instructions for every agent. Codex and other AGENTS.md-aware tools read it directly; Claude Code reads it through the `@AGENTS.md` import in `CLAUDE.md`. Put instructions here, never in `CLAUDE.md`.

## Working Style

The repository owner develops hands-on. By default, **investigate and explain; do not modify code.** When asked about a bug or behavior, report the root cause and the relevant files/lines, and leave the implementation to the owner unless explicitly asked to change it.

Filing a GitHub Issue needs no prior confirmation: create it and report the URL. Editing issue bodies, creating PRs, and pushing still need confirmation.

## Output Language

**Write all output to the repository owner in Japanese**: investigation reports, review findings, commit messages, PR titles and bodies. Identifiers, type names, file paths, and code stay verbatim.

## Overview

Flow aims to be a writing agent: it runs the whole pipeline from planning to proofreading as an agent loop, the way a coding agent iterates on code. The reference experience is the report feature of NotebookLM (source-grounded, multi-step generation). It is a .NET 10 solution (`Flow.slnx`, XML solution format) at the skeleton stage. Projects live under `src/`. Design decisions live in `docs/design.md`; read it before changing the pipeline or the acceptance-check layers.

Current projects:

- `src/Flow.CLI`: console app (see `src/Flow.CLI/AGENTS.md`)

## Commands

Run from the repository root. Paths are given in `C:/...` form because the Bash tool is Git Bash.

```bash
dotnet build Flow.slnx
```

There is no test project yet. When one is added, register it in `Flow.slnx` under the `/src/` folder and run it with `dotnet test Flow.slnx` (single test: `dotnet test --filter "FullyQualifiedName~<TestName>"`).

## Conventions

- Agent instructions are layered like a Pandekten code: **general provisions** (this file, rules common to the whole repository) -> **project provisions** (one `AGENTS.md` per `.csproj` directory, plus a `CLAUDE.md` stub containing only `@AGENTS.md`) -> **special provisions** (an `AGENTS.md` below a csproj, exceptional). The rules that apply to a file are the sum of all layers above it. Claude Code loads a subdirectory `CLAUDE.md` lazily when it touches files there, so project-specific detail belongs in the project's `AGENTS.md`, not here.
- A lower layer writes only its difference from the layers above: it specializes or adds constraints and never repeats them. If a lower rule contradicts an upper rule, the lower rule applies within its directory and the contradiction is a docs bug: report it and fix one side in the same change.
- Add special provisions below a csproj only when that directory has an invariant of its own that the project `AGENTS.md` cannot express without leaking into sibling directories, and the need exists now rather than as a possibility. Never place one just because a directory exists.
- Each project `AGENTS.md` has three fixed sections: **Scope** (what the project owns and does not own), **Review Points** (what to suspect when reviewing changes there), **Acceptance Conditions** (contracts that must stay true after every change; each is meant to become a test that fails when the contract breaks). Per-task completion criteria belong to the GitHub Issue, not here.
- Review Points and Acceptance Conditions are two views of one list, split by whether the contract is automated yet. When a review point becomes a test, move it to Acceptance Conditions and delete it from Review Points. What stays in Review Points permanently is what needs human judgment (scope, over-abstraction). A growing Review Points list is a sign that testable contracts are not being tested.
- **A C# namespace and its physical directory must match**, and must be changed together (`src/Flow.CLI/Core/Judges/X.cs` -> `namespace Flow.Core.Judges;`). Do not create a mismatch by moving only one side.
- Adding a project: place it under `src/`, add a `<Project Path="src/<Name>/<Name>.csproj" />` entry inside the `/src/` folder in `Flow.slnx`, and create the `AGENTS.md` / `CLAUDE.md` pair (copy the stub from `src/Flow.CLI/CLAUDE.md`).
- `bin/` and `obj/` are gitignored. `.idea/` (Rider) is untracked and should stay out of commits.

## Workflow: issue-driven

- Development is issue-driven. When a design discussion reaches a conclusion, file it as a GitHub Issue (`gh issue create`) before writing code. The issue holds the decision and its acceptance criteria; `docs/design.md` keeps the durable picture and links to issues where relevant.
- Work on a branch per issue. Branch name: `<issue-number>-<slug>` (lowercase ASCII and hyphens; add `-<area>-` after the number once area labels exist). **Never put the name of the agent or tool that did the work in the branch name** (`codex/`, `claude/`, `agent/`): a branch says what changes, not who changed it. `main` is not pushed to directly.
- PR body contains `Closes #<issue>`. **Do not mix unrelated refactoring, renames, cleanup, or dependency changes into a PR**; report them and let the owner decide (issue / `// TODO:` / nothing).
- Docs-only changes (`AGENTS.md`, `docs/`) may skip the issue and go straight to a branch and PR.
- CI (`.github/workflows/ci.yml`) builds on every PR and push to main. Add `dotnet test` steps when a test project exists. Do not report "CI passed" while a run is queued or in progress.
- Test naming, once tests exist: `MemberName_<condition>_<expected>` (member name in English, condition and expected result written in Japanese; class names mirror the type under test). Every bug fix ships with a regression test.
- Open questions listed under the open-questions section of `docs/design.md` become issues when they are picked up, one issue per question.
