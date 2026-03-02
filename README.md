<div align="center">
    <h1>🧭 RPI (Research, Plan, Implement) Workflow</h1>
    <h3><em>Build predictable software with a minimal, efficient AI-guided workflow.</em></h3>
</div>

<p align="center">
    <strong>RPI workflow prompts for collaborating with AI agents with minimal documentation overhead.</strong>
</p>

<p align="center">
    <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"/></a>
    <img src="https://img.shields.io/badge/Documentation_Reduction-70%25-green.svg" alt="Documentation Reduction"/>
    <img src="https://img.shields.io/badge/Workflow-Research_Plan_Implement-blue.svg" alt="RPI Workflow"/>
</p>

## Overview

This repository provides **lightweight prompts** (Markdown files) that guide AI assistants through a streamlined software development workflow:

- **Research**: explore codebase and identify patterns (~100 lines, temporary)
- **Validate Research**: score research against FAR rubric before planning
- **Plan**: break work into actionable tasks (temporary)
- **Validate Plan**: score plan against FACTS rubric before implementing
- **Implement**: execute tasks with verification and quality gates
- **Recap**: auto-generate summary and verify implementation (max 40 lines, permanent)

RPI achieves **70%+ reduction in documentation** compared to traditional spec-driven approaches while maintaining code quality.

## Table of Contents

- [TLDR / Quickstart](#tldr--quickstart)
- [Details for the 6-step workflow](#details-for-the-6-step-workflow)
- [Orchestrated Workflow (rpi-orchestrate)](#orchestrated-workflow-rpi-orchestrate)
- [Artifacts and directory layout](#artifacts-and-directory-layout)
- [Documentation](#documentation)
- [Context verification markers](#context-verification-markers)
- [Security best practices](#security-best-practices)
- [Contributing](#contributing)
- [References](#references)
- [License](#license)

## TLDR / Quickstart

### Installation options

#### Option A: Install as Slash Commands (Recommended)

Install these prompts as native `/slash-commands` in your AI assistant (Cursor, Windsurf, Claude Code, etc.) using the [slash-command-manager](https://github.com/liatrio-labs/slash-command-manager) utility:

**Prerequisite:** `uvx` comes from [uv](https://docs.astral.sh/uv/). Install uv first if you don’t already have it:

- (Mac): `brew install uv`
- (Windows): `winget install astral-sh.uv`

##### Install RPI w/ Bash (Mac)

```bash
uvx --from git+https://github.com/liatrio-labs/slash-command-manager \
  slash-man generate \
  --github-repo <your-org>/rpi-workflow \
  --github-branch main \
  --github-path prompts/
```

##### Install RPI w/ PowerShell (Windows)

```ps
uvx --from git+https://github.com/liatrio-labs/slash-command-manager `
  slash-man generate `
  --github-repo <your-org>/rpi-workflow `
  --github-branch main `
  --github-path prompts/
```

**What this command does:**

- `uvx` runs a Python tool without installing it globally (like `npx` for Python)
- Fetches the `slash-command-manager` tool from GitHub
- Auto-detects your installed AI assistants from the list of supported tools
- Downloads the prompt files for each supported tool from the `prompts/` directory
- Installs them as slash commands for each supported tool

**Result:** you can now type `/rpi-research` in your AI assistant to start the workflow.

**Where to use the slash commands:** in AI chat UIs (e.g., Windsurf, Claude Code) type `/` in the chat input. Some AI assistants require being in "Agent" or "Code" mode for slash commands to appear.

#### Option B: Manual Copy-Paste (No Installation)

Copy the contents of a prompt file directly from `prompts/` and paste it into your AI chat. The AI will follow the structured instructions in the prompt.

### Quick "try it" flow

1. Run `/rpi-research` to explore the codebase and identify patterns for your feature.
2. Run `/rpi-validate-research` to score findings against the FAR rubric before planning.
3. Run `/rpi-plan` to create an actionable implementation plan with tasks.
4. Run `/rpi-validate-plan` to score the plan against the FACTS rubric before implementing.
5. Run `/rpi-implement` to execute tasks with quality gates (no auto-commits - you verify first).
6. Run `/rpi-recap` to generate a permanent summary and validate the implementation.

**Or run the full pipeline automatically:**

```bash
/rpi-orchestrate "Your feature description"
```

See [Orchestrated Workflow](#orchestrated-workflow-rpi-orchestrate) for details.

## Details for the 6-step workflow

Each step uses a different prompt file. RPI uses **temporary artifacts** (deleted after implementation) and **permanent summaries** (committed to repo).

1. **Research** ([`prompts/rpi-research.md`](./prompts/rpi-research.md))
   - **What it does**: explores codebase using parallel agents, identifies patterns, scores findings via FAR Scale
   - **Output**: `.rpi/[feature-name]/research.md` (~100 lines, AI-optimized, **temporary**)
   - **Why**: builds context for implementation without permanent documentation overhead

2. **Validate Research** ([`prompts/rpi-validate-research.md`](./prompts/rpi-validate-research.md))
   - **What it does**: scores research against the FAR rubric (Factual ≥4, Actionable ≥3, Relevant ≥3, mean ≥4.0)
   - **Output**: pass/fail evaluation with recommendations (no files written, **in-chat only**)
   - **Why**: catches weak research before it produces a weak plan

3. **Plan** ([`prompts/rpi-plan.md`](./prompts/rpi-plan.md))
   - **What it does**: creates structured task hierarchy with parent tasks (1.0, 2.0, 3.0) and sub-tasks (1.1, 1.2, 1.3)
   - **Output**: `.rpi/[feature-name]/plan.md` (structured format, **temporary**)
   - **Why**: provides actionable implementation roadmap for AI execution

4. **Validate Plan** ([`prompts/rpi-validate-plan.md`](./prompts/rpi-validate-plan.md))
   - **What it does**: scores plan against the FACTS rubric (Feasibility, Atomicity, Clarity, Testability, Size — mean ≥3.0)
   - **Output**: pass/fail evaluation with recommendations (no files written, **in-chat only**)
   - **Why**: catches weak plans before they produce broken implementations

5. **Implement** ([`prompts/rpi-implement.md`](./prompts/rpi-implement.md))
   - **What it does**: executes sub-tasks sequentially with quality gates (build, lint, test); supports Continuous / Task / Batch checkpoint modes
   - **Output**: working code + updated `.rpi/[feature-name]/plan.md` with progress markers
   - **Why**: systematic implementation with built-in verification (no auto-commits - you verify first)

6. **Recap** ([`prompts/rpi-recap.md`](./prompts/rpi-recap.md))
   - **What it does**: validates implementation against plan, auto-generates high-level "what/why" summary
   - **Output**: `docs/rpi/[feature-name].md` (max 40 lines, human-readable, **committed to repo**)
   - **Why**: provides human-focused documentation while cleaning up temporary AI artifacts

7. **SHIP IT** 🚢💨 (after you verify and commit)

## Orchestrated Workflow (rpi-orchestrate)

[`prompts/rpi-orchestrate.md`](./prompts/rpi-orchestrate.md) runs the full Research → Validate → Plan → Validate → Implement → Recap pipeline automatically using a **Claude Agent Team**. Instead of running each step manually, the orchestrator spawns specialist teammates, gates on validation results, and hands off between phases.

### Usage

```bash
/rpi-orchestrate "Add user authentication with JWT"
```

### Gate Modes

Choose how much oversight you want when prompted:

| Mode | Behavior |
| :--- | :------- |
| **Gated** *(default)* | Pauses after Research and Plan validation — you explicitly approve before each phase continues |
| **Smart** | Auto-advances if FAR mean ≥ 4.0 and FACTS mean ≥ 3.0 — only pauses on failures or at implementation review |
| **Auto** | Runs all phases end-to-end — stops only if a validation or implementation check fails |

Regardless of gate mode, the orchestrator **always pauses** on any validation failure (FAR or FACTS) and any implementation failure.

### Team Structure

| Teammate | Skill invoked |
| :------- | :------------ |
| Researcher | `/rpi-research` |
| Validator | `/rpi-validate-research`, then `/rpi-validate-plan` |
| Planner | `/rpi-plan` |
| Implementer | `/rpi-implement` (Batch Mode) |
| Recapper | `/rpi-recap` |

### Prerequisites

- Claude Code with agent team support
- All `rpi-*` slash commands installed (see [Installation](#installation-options))

---

## Highlights

- **Prompt-first workflow:** Use curated prompts to go from idea → research → validate → plan → validate → implement → recap.
- **Built-in quality gates:** FAR rubric validates research quality; FACTS rubric validates plan quality before implementation begins.
- **70% documentation reduction:** Temporary artifacts are deleted after use—only high-level summaries are committed.
- **No auto-commits:** You verify functionality before committing—AI doesn't push broken code.
- **No dependencies required:** The prompts are plain Markdown files that work with any AI assistant.
- **Context verification:** Built-in emoji markers (RPI1️⃣, RPI✅, RPI2️⃣, RPI3️⃣, RPI4️⃣) detect when AI responses follow critical instructions, helping identify context rot issues early.

## Why RPI Workflow?

The RPI (Research, Plan, Implement) workflow achieves **70%+ documentation reduction** compared to traditional spec-driven approaches while maintaining code quality. It treats Research and Plan artifacts as **temporary scaffolding** rather than permanent documentation. This repository provides lightweight, prompt-centric guidance that helps AI assistants explore, plan, and implement features efficiently—without drowning teams in documentation overhead. By separating temporary AI context from permanent human documentation, RPI delivers faster development cycles while keeping your repository clean.

## Guiding Principles

- **Research before planning:** Explore the codebase to understand patterns and architecture before creating an implementation plan.
- **Temporary scaffolding:** Research and Plan artifacts are AI-optimized context that gets deleted after implementation—not permanent documentation.
- **Permanent summaries only:** Only high-level "what/why" summaries (max 40 lines) are committed—reducing documentation-to-code ratio from 13:1 to 2:1.
- **No auto-commits:** AI implements and verifies, but you control when code gets committed—preventing broken commits and giving you control over git history.
- **Progressive implementation:** Execute sub-tasks sequentially (1.1, 1.2, 1.3) with verification at each step.
- **Human approval gates:** Quick approval at Research/Plan stages—not line-by-line code review during implementation.

## Artifacts and directory layout

RPI uses two types of documentation with different lifecycles:

### Temporary artifacts (gitignored)

AI-focused context stored in per-feature subdirectories under `.rpi/` during workflow execution:

- **Research findings:** `.rpi/[feature-name]/research.md` (~100 lines, minimal markdown, AI-optimized)
- **Implementation plan:** `.rpi/[feature-name]/plan.md` (structured task hierarchy with 1.0, 1.1, 1.2 format)

These files are no longer needed after the Recap phase. They're optimized for AI parsing, not human consumption.

### Permanent artifacts (committed)

Human-focused documentation stored alongside code or in `docs/`:

- **Implementation summaries:** `docs/rpi/[feature-name].md` (max 40 lines, high-level "what/why")
- **Working code:** Your actual implementation files

Example directory structure during workflow:

```bash
.rpi/                          # Gitignored temporary artifacts
└── auth-feature/
    ├── research.md            # Research findings (temporary)
    └── plan.md                # Implementation plan (temporary)

docs/rpi/                      # Committed permanent documentation
└── auth-feature.md           # High-level summary (permanent)

src/                           # Your working code
└── auth/                      # Implementation files
```

**Key philosophy:** Documentation-to-code ratio drops from **13:1 (traditional) to 2:1 (RPI)** by treating planning artifacts as temporary scaffolding.

## Documentation

### External References

- **[Goose RPI Tutorial](https://block.github.io/goose/docs/tutorials/rpi/)** — Tutorial on RPI workflow concepts
- **[Context Engineering: RPI Framework](https://fanpino.com/en/blog/context-engineering-rpi-workflow-ai-coding/)** — Detailed article on RPI approach
- **[Spec-Driven Development Playbook](https://liatrio-labs.github.io/spec-driven-workflow/)** — The predecessor workflow that inspired RPI

### Getting help

- **Ask/Report**: Open a GitHub Issue in this repo with details about your environment and AI assistant

## Context verification markers

Each prompt includes a context verification marker that appears at the start of AI responses. These markers help detect **context rot**—a phenomenon where AI performance degrades as input context length increases, even when tasks remain simple.

| Prompt | Marker |
|---|---|
| `rpi-orchestrate` | RPI⚡ |
| `rpi-research` | RPI1️⃣ |
| `rpi-validate-research` | RPI✅ |
| `rpi-plan` | RPI2️⃣ |
| `rpi-validate-plan` | RPI✅ |
| `rpi-implement` | RPI3️⃣ |
| `rpi-recap` | RPI4️⃣ |

**Why this matters:** Context rot doesn't announce itself with errors. It creeps in silently, causing models to lose track of critical instructions. When you see the marker at the start of each response, it's an <strong>indicator</strong> that the AI is probably following the prompt's instructions. If the marker disappears, it's an immediate signal that context instructions may have been lost.

**What to expect:** You'll see responses like `RPI1️⃣ I'll explore the codebase to identify patterns...` or `RPI3️⃣ Let me implement task 1.1...`. This is normal and indicates the verification system is working.

## Security Best Practices

### Protecting Sensitive Data in Committed Artifacts

Permanent summary documents (from the Recap phase) are committed to your repository and may be publicly visible. **Never commit real credentials or sensitive data.** Follow these guidelines:

- **Replace credentials with placeholders**: Use `[YOUR_API_KEY_HERE]`, `[REDACTED]`, or `example-key-123` instead of real API keys, tokens, or passwords
- **Use example values**: When demonstrating configuration, use dummy or example data instead of production values
- **Sanitize command output**: Review CLI output and logs for accidentally captured credentials before committing
- **Consider pre-commit hooks**: Tools like [gitleaks](https://github.com/gitleaks/gitleaks), [truffleHog](https://github.com/trufflesecurity/truffleHog), or [talisman](https://github.com/thoughtworks/talisman) can automatically scan for secrets before commits

The RPI workflow prompts include built-in reminders about security, but ultimate responsibility lies with the developer to review artifacts before committing or pushing to remotes.

## Contributing

Open a GitHub Issue to report bugs, suggest improvements, or ask questions.

## References

| Reference | Description | Link |
| --- | --- | --- |
| Goose RPI Tutorial | Official tutorial on Research, Plan, Implement workflow concepts. | [block.github.io/goose/docs/tutorials/rpi/](https://block.github.io/goose/docs/tutorials/rpi/) |
| Context Engineering Article | Deep dive into RPI framework for AI-assisted coding. | [fanpino.com/en/blog/context-engineering-rpi-workflow-ai-coding/](https://fanpino.com/en/blog/context-engineering-rpi-workflow-ai-coding/) |
| Slash Command Manager | Utility for installing prompts as slash commands in AI assistants. | [liatrio-labs/slash-command-manager](https://github.com/liatrio-labs/slash-command-manager) |
| Spec-Driven Development | The predecessor workflow that RPI evolved from. | [liatrio-labs/spec-driven-workflow](https://github.com/liatrio-labs/spec-driven-workflow) |
| MCP | Standard protocol for AI agent interoperability. | [modelcontextprotocol.io](https://modelcontextprotocol.io/docs/getting-started/intro) |

## License

This project is licensed under the Apache License, Version 2.0. See the [LICENSE](LICENSE) file for details.
