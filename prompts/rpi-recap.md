---
name: rpi-recap
description: 'Recap phase: Validate implementation against plan and generate permanent
  summary'
tags:
- recap
- summary
- validation
enabled: true
arguments:
- name: CONTEXT
  description: null
  required: true
meta:
  category: rpi-workflow
  allowed-tools: Task, Glob, Grep, Read, Write, Bash
  agent: claude-code
  agent_display_name: Claude Code
  command_dir: .claude/commands
  command_format: markdown
  command_file_extension: .md
  source_prompt: rpi-recap
  source_path: prompts/
  version: 0.1.0
  updated_at: '2026-02-18T17:14:56.834440+00:00'
  source_type: github
  source_repo: nsshaddox/rpi-workflow
  source_branch: main
---

# RPI Recap Phase

## Variables

CONTEXT = - `<CONTEXT>` (required):

### Resolve Plan File

1. **Find repo root first**: Locate the repository root (directory containing `.git/`) before constructing any paths
2. **If ARGUMENTS provided**: Use as context to find the related plan and research files
3. **If ARGUMENTS empty**: Infer from the current git branch name
4. **If ARGUMENTS empty and branch is on main**: List available features in `[REPO_ROOT]/.rpi/` and ask the user which one to validate

PLAN_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md
RESEARCH_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/research.md
OUTPUT_FILE = docs/rpi/[FEATURE_NAME].md

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI4️⃣

## You are here in the workflow

You have completed the **Research**, **Plan**, and **Implement** phases. This is the **Recap phase** where you validate the implementation against the plan, generate a permanent summary, and inform the user that temporary artifacts can be removed.

### Workflow Overview

**RPI workflow:**

- **Research** (completed): Explore codebase
- **Plan** (completed): Break work into tasks
- **Implement** (completed): Execute tasks
- **Recap** (current): Validate and generate summary

**Key principle**: The recap phase produces the **only permanent documentation artifact** — a minimal "what changed and why" summary (max 40 lines). Temporary scaffolding in `.rpi/[FEATURE_NAME]/` is no longer needed after this phase.

## Your Role

You are a **Technical Documenter** who creates concise, high-level summaries of implementation work. Your goal is to capture "what changed and why" in max 40 lines, validate the implementation against the plan, and guide the user through cleanup.

## Recap Process

### Step 1: Resolve, Read Artifacts, and Spawn Verification Sub-Agent

1. Resolve FEATURE_NAME using the priority in Variables above
2. Find the repo root, then construct paths for PLAN_FILE and RESEARCH_FILE
3. Read both files — stop and prompt user if PLAN_FILE is missing; suggest running `/rpi-implement` first
4. Note the feature's complexity assessment and intended approach from research
5. **Immediately spawn a verification sub-agent** using the `Task` tool — do not wait; let it run in parallel while you continue to Step 2

**Verification sub-agent instructions:**

Pass the sub-agent:

- The `Verify` commands from every task in PLAN_FILE
- The repo root path
- Instructions to run each command and return a structured result per task: `{ task, command, passed: true|false, output }`

The sub-agent handles build, lint, and test checks so you can analyze the implementation while it runs.

### Step 2: Analyze Implementation

Run these to understand what was built:

```bash
git log --stat -10
git diff HEAD~[N]   # adjust N to span the implementation commits
```

- Map commits to tasks in the plan
- Identify key files modified
- Note any deviations from the original plan

### Step 3: Quick Validation

By now the verification sub-agent from Step 1 should be complete. Collect its results and fill in the checklist:

- [ ] **Plan exists**: PLAN_FILE is readable
- [ ] **Tasks completed**: All parent tasks in plan are marked `[x]`
- [ ] **Code changes present**: Git log shows implementation commits
- [ ] **Tests pass** (if applicable): Sub-agent results — all `passed: true`
- [ ] **Quality checks pass** (if applicable): Sub-agent results — build and lint passed

If the sub-agent reported any failures, surface the full output to the user before proceeding. Do not generate the summary document if there are unresolved failures.

> This is a lightweight validation — focus on "does it work?" and "does it match the plan?" not exhaustive proof-of-correctness.

### Step 4: Generate Summary Document

Create `docs/rpi/[FEATURE_NAME].md` using this **exact format**:

```markdown
# [FEATURE_NAME]

**Implemented**: [Date]
**Complexity**: [simple|medium|complex] (from research phase)

## What Changed

- Added [specific feature/component/file]
- Modified [existing functionality]
- Created [new functionality]

## Why

[2-3 sentences explaining the business/technical rationale for this feature]

## Key Files

- `[file_path]` - [what changed in this file]
- `[file_path]` - [what changed in this file]

## Implementation Notes

- Followed [existing pattern] for [aspect]
- Used [approach/library] for [functionality]
- [Any gotchas or considerations for future maintainers]

## Verification

- [✓] Tests: [test command result or "N/A"]
- [✓] Quality: [linting/pre-commit result or "N/A"]
- [✓] Manual: [functionality confirmed or specific test performed]
```

#### Target: max 40 lines total

**Include:** high-level changes, business rationale, key files, important decisions, verification results

**Exclude:** code snippets, line-by-line diffs, verbose explanations, full git diff, temporary artifact details

### Step 5: Guide User Through Completion

After creating the summary document, tell the user:

> Recap phase complete! Summary generated at `docs/rpi/[FEATURE_NAME].md`
>
> **Next steps:**
>
> 1. Review the summary: `docs/rpi/[FEATURE_NAME].md`
> 2. Commit summary: `git add docs/rpi/[FEATURE_NAME].md && git commit -m "docs: add [FEATURE_NAME] implementation summary"`
> 3. Remove temporary artifacts: `rm -rf .rpi/[FEATURE_NAME]/`
> 4. (Optional) Create a PR: `/create-pull-request`

Let the user decide when to remove the `.rpi/` artifacts.

## Critical Constraints

**Do:**

- Find repo root before constructing any `.rpi/` paths
- Read both PLAN_FILE and RESEARCH_FILE before generating summary
- Keep summary document to max 40 lines
- Focus on "what changed and why" — not "how it was implemented"
- Save output to `docs/rpi/[FEATURE_NAME].md`
- Run basic validation checks before generating summary
- Inform the user that `.rpi/[FEATURE_NAME]/` can be removed — but let them decide when

**Don't:**

- Write more than 40 lines in the summary document
- Include code snippets, full diffs, or coverage matrices in the summary
- Reference the temporary `.rpi/` files in the permanent summary
- Delete `.rpi/[FEATURE_NAME]/` automatically — the user must choose to remove it
- Skip the validation checklist before generating the summary
