# AI Dev Playbook

A plug-and-play template for AI-assisted software development. Designed for team leads integrating into new teams, this playbook improves code quality and productivity by giving AI coding assistants precise, high-signal instructions.

## What's Inside

- **Copilot Instructions** — Core principles and architecture guidelines loaded into every AI conversation
- **File-Scoped Instructions** — Language and testing standards that auto-apply based on file type
- **Custom Agents** — Specialized AI agents for architecture, code review, incident resolution, and more
- **Reusable Prompts** — Pre-built prompts for common tasks (unit tests, code review, reporting)
- **Profile Support** — Pre-configured setups for Backend (.NET), Frontend (Angular), and Full-Stack

## Quick Start

1. **Copy `.github/`** to your project root

2. **Pick a profile** from `profiles/` and copy it to `.github/copilot-instructions.md`:
   - `backend-dotnet.md` — .NET backend projects
   - `frontend-angular.md` — Angular frontend projects
   - `fullstack.md` — .NET + Angular full-stack projects (default)

3. **Customize** the `<!-- CONFIGURE -->` sections with your project details

4. **Commit and push**

> The `.github/copilot-instructions.md` included in `.github/` is the **fullstack** profile by default. Replace it with another profile if needed.

## Updating

After template improvements, sync to your project:

```bash
# From your project root — copies shared files, preserves your copilot-instructions.md
cp -r /path/to/ai-development-playbook/.github/instructions/ .github/instructions/
cp -r /path/to/ai-development-playbook/.github/agents/ .github/agents/
cp -r /path/to/ai-development-playbook/.github/prompts/ .github/prompts/
```

Or use git submodules for automatic syncing — see [docs/setup.md](docs/setup.md).

## Company → Product Hierarchy

This template supports a layered override model:

```
ai-development-playbook (universal standards)
  └── Company-level overrides (your org standards)
       └── Product-level overrides (project-specific)
```

See [docs/hierarchy.md](docs/hierarchy.md) for details.

## Agents

| Agent | Description |
|---|---|
| `@architect` | Orchestrates complex tasks through a multi-agent pipeline: analyze → implement → review → verify |
| `@reviewer` | Deep code review with structured findings and traceability |
| `@idea-stress-test` | Stress-tests ideas by finding flaws, risks, and edge cases |
| `@incident` | End-to-end incident resolution with root cause analysis |
| `@documenter` | Generates comprehensive repository documentation |

## Prompts

| Prompt | Description |
|---|---|
| `/unit-tests` | Generate high-quality unit tests for selected code |
| `/code-review` | Review code for quality, security, and architectural compliance |
| `/plandek-report` | Generate engineering metrics report (opt-in, activated on request) |

## Repository Structure

```
ai-development-playbook/
├── README.md
├── .gitignore
├── _prompt/generation-prompt.md           # Original prompt — regenerate from scratch
│
├── .github/                               # ← Copy this folder to any project
│   ├── copilot-instructions.md            # Fullstack profile (default)
│   ├── instructions/
│   │   ├── csharp.instructions.md         # Auto-applies to *.cs
│   │   ├── angular.instructions.md        # Auto-applies to *.ts
│   │   ├── testing-dotnet.instructions.md # Auto-applies to *Tests*/*.cs
│   │   └── testing-angular.instructions.md# Auto-applies to *.spec.ts
│   ├── agents/
│   │   ├── architect.agent.md             # Multi-agent pipeline orchestrator
│   │   ├── analyzer.agent.md              #   └─ Request analysis (internal)
│   │   ├── implementer.agent.md           #   └─ Implementation (internal)
│   │   ├── reviewer.agent.md              # Code review (standalone or pipeline)
│   │   ├── idea-stress-test.agent.md      # Stress-test ideas (devil's advocate)
│   │   ├── incident.agent.md              # Incident resolution
│   │   └── documenter.agent.md            # Generate repo documentation
│   └── prompts/
│       ├── unit-tests.prompt.md
│       ├── code-review.prompt.md
│       └── plandek-report.prompt.md       # Opt-in metrics report
│
├── profiles/                              # Pick one → copy as copilot-instructions.md
│   ├── backend-dotnet.md                  # .NET + Clean Architecture
│   ├── frontend-angular.md                # Angular + Signals + Standalone
│   └── fullstack.md                       # Both combined
│
└── docs/
    ├── setup.md                           # Deployment options
    └── hierarchy.md                       # Company → Product override model
```

## Design Principles

- **Less is more** — Every instruction earns its place. No filler, no conflict.
- **Unlock, don't constrain** — Guide the AI to think like a senior engineer, not follow a script.
- **Self-selecting** — Instructions auto-apply based on file type. No manual toggling.
- **Project-agnostic** — Works for any .NET/Angular project out of the box.
- **Hierarchical** — Company → Product layering without duplication.

## License

MIT
