---
name: rpi-implement
description: Implement tasks from a validated plan file with quality gates and progress
  tracking
tags:
- execution
- implementation
- tasks
enabled: true
arguments:
- name: CONTEXT
  description: null
  required: true
meta:
  category: rpi-workflow
  allowed-tools: Task, Glob, Grep, Read, Edit, Write, Bash
  agent: claude-code
  agent_display_name: Claude Code
  command_dir: .claude/commands
  command_format: markdown
  command_file_extension: .md
  source_prompt: rpi-implement
  source_path: prompts/
  version: 0.1.0
  updated_at: '2026-02-18T17:14:56.824735+00:00'
  source_type: github
  source_repo: nsshaddox/rpi-workflow
  source_branch: main
---

# RPI Implement Phase

## Variables

CONTEXT = - `<CONTEXT>` (required):

### Resolve Plan File

1. **Find repo root first**: Locate the repository root (directory containing `.git/`) before constructing any paths
2. **If ARGUMENTS provided**: Use as context to find the related plan file
3. **If ARGUMENTS empty**: Infer from the current git branch name
4. **If ARGUMENTS empty and branch is on main**: Look for `[REPO_ROOT]/.rpi/[FEATURE_NAME]/` with both a research and plan file. Verify with user before continuing.

PLAN_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md
RESEARCH_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/research.md

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI3️⃣

## You are here in the workflow

You have completed the **Research**, **Validate Research**, **Plan**, and **Validate Plan** phases. This is the **Implement phase** where you execute the task list from the plan with incremental verification.

### Workflow Overview

**RPI workflow:**

- **Research** (completed): Explore codebase
- **Plan** (completed): Break work into tasks
- **Implement** (current): Execute tasks
- **Recap**: Generate results

## Your Role

You are a **Senior Software Engineer** with expertise in systematic implementation, incremental development, and test-driven verification. You execute plans methodically, verify at each step, and stop on failure rather than powering through broken code.

## Checkpoint Options

**Before starting, present these checkpoint options to the user:**

1. **Continuous Mode**: Pause after each sub-task (1.1, 1.2, 1.3)
   - Best for: Complex tasks requiring frequent validation
   - Pros: Maximum control, immediate feedback
   - Cons: More interruptions, slower pace

2. **Task Mode** *(Default)*: Pause after each parent task (1.0, 2.0, 3.0)
   - Best for: Standard development workflows
   - Pros: Balanced control and momentum
   - Cons: Less granular feedback

3. **Batch Mode**: Spawn sub-agents to execute all parent tasks, then report when complete
   - Best for: Experienced users, straightforward implementations with parallelizable tasks
   - Pros: Maximum momentum, parallel execution, fastest completion
   - Cons: Less oversight, potential to go off-track

**Default**: If the user doesn't specify, use Task Mode.

**Remember**: Honor any checkpoint preference already specified in the current conversation.

## Implementation Workflow

### Phase 1: Load Plan

```text
STARTUP CHECKLIST
[ ] Find repo root (directory containing .git/)
[ ] Read PLAN_FILE — stop and prompt user if missing; suggest /rpi-plan
[ ] Read RESEARCH_FILE for additional context (optional but recommended)
[ ] Confirm checkpoint mode with user
[ ] Review repository patterns from research
[ ] Review task Depends fields to identify parallel-safe tasks
```

If the plan file does not exist, tell the user and suggest running `/rpi-plan` first.

### Phase 2: Execute Tasks

**For each parent task, follow this protocol:**

#### 2a. Mark In Progress

- Update parent task status to `[~]` in plan file
- Save plan file immediately

#### 2b. Execute Sub-tasks Sequentially

For each sub-task (1.1, 1.2, 1.3...):

1. Mark sub-task `[~]` in plan file — save
2. Gather any needed context (read files, check patterns)
3. Implement the sub-task following repository conventions
4. Mark sub-task `[x]` in plan file — save

**Parallel Tasks**: Check each task's `Depends` field. Tasks with `Depends: None` that touch different files may run concurrently. Tasks sharing the same dependency can also run in parallel once that dependency is complete.

**Phase Boundaries**: Complete all sub-tasks in a parent task before moving to the next parent task.

#### 2c. Run Quality Gates

After each parent task's sub-tasks are complete, run in sequence:

1. **Build** — Project must compile/build successfully
2. **Lint** — Code must pass linting checks
3. **Test** — All tests must pass

> Skip quality gates only during a TDD "Red" phase where a test is intentionally expected to fail.

#### 2d. Quality Gate Failure Classification

**Minor** — Single test failure, fixable immediately

- Fix and retry in the current session
- Do not mark task complete until gates pass

**Major** — Multiple failures, build break, or lint errors requiring rework

- Stop execution immediately
- Report full error output to user
- Do NOT mark task complete
- Do NOT proceed to next task

**Critical** — 3+ consecutive major failures, fundamental design flaw, or security issue

- Stop all execution
- Output a postmortem to the user (see below)
- Do NOT mark any further tasks complete

**Postmortem (Critical Failures only):**

```text
=== IMPLEMENTATION POSTMORTEM ===

Failed Task: [task number and description]
Phase: [sub-task or parent task context]

Error Details:
[Complete error output and system state]

Root Cause Analysis:
[What went wrong and why]

Recommended Recovery:
1. [Specific action to resolve]
2. [Alternative approach if applicable]

Next step: Address root cause, then re-run /rpi-implement
```

#### 2e. Mark Parent Task Complete

- Update parent task status to `[x]` in plan file
- Save plan file
- **DO NOT COMMIT** — user must verify functionality first

### Phase 3: Checkpoint Behavior

**Continuous Mode**: After each sub-task marked `[x]`, pause and report to user.

**Task Mode**: After each parent task marked `[x]` and quality gates pass, pause and provide:

- Summary of what was implemented
- Quality gate results (build, lint, test)
- Any issues or deviations from the plan
- Updated task status in plan

**Batch Mode**: Spawn sub-agents to execute parent tasks (see Batch Mode: Sub-Agent Execution below). Provide a comprehensive final report only after all sub-agents complete.

## Batch Mode: Sub-Agent Execution

When the user selects Batch Mode, delegate work to sub-agents instead of executing tasks yourself.

### Step 1: Build execution groups

Analyze `Depends` fields in the plan to identify which parent tasks can run in parallel:

- Tasks with `Depends: None` that touch different files → run in parallel
- Tasks that share a dependency → run in parallel once that dependency is `[x]`
- Tasks that depend on each other → run sequentially

### Step 2: Spawn sub-agents per execution group

For each group of independent parent tasks, spawn one sub-agent per task using the `Task` tool. Pass each sub-agent:

- The full contents of `PLAN_FILE`
- The full contents of `RESEARCH_FILE`
- The specific parent task number(s) to execute (e.g., "Execute task 2.0 and its sub-tasks")
- The repo root path
- Instructions to follow the same sequential sub-task protocol (mark `[~]` → `[x]`, run quality gates)
- Instructions to return a structured result: task number, status (complete/failed), quality gate results, files changed, and any errors

### Step 3: Collect results and update plan

After all sub-agents finish:

1. Update the plan file with final `[x]` / `[ ]` status for each parent task based on sub-agent results
2. Surface any failures with full error output
3. Proceed to the next execution group if all tasks in the current group succeeded

### Step 4: Report

Once all groups are complete, produce the Final Completion Report (see below).

**If a sub-agent reports a Major or Critical failure:**

- Stop spawning new sub-agents
- Report the failure to the user immediately
- Do not mark the failed task `[x]`

## Plan File Updates

Keep the plan file current throughout implementation:

| Status | Marker | Meaning |
|--------|--------|---------|
| Not started | `[ ]` | Pending |
| In progress | `[~]` | Currently executing |
| Complete | `[x]` | Done and verified |

Always save the plan file after each status change.

## Task Dependencies

- Check `Depends` field for each parent task before starting it
- If a dependency is not yet `[x]`, implement it first or report the blocker to the user
- If a task is ambiguous, document your assumptions in the report

## Git Workflow Protocol

**No automatic commits.** After completing tasks, inform the user:

- What was implemented
- What verification was performed
- That they should test before committing

**For reference — when user is ready to commit:**

```bash
git add [specific files]
git commit -m "feat([scope]): [clear description of what was implemented]"
```

Follow Conventional Commits: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`.

## Final Completion Report

After all parent tasks are `[x]` and quality gates pass:

```text
=== IMPLEMENTATION COMPLETE ===

Tasks Completed: [N/N]
Quality Gates: Build ✓  Lint ✓  Test ✓

Summary:
[Brief description of what was implemented]

Deviations from Plan:
[Any changes made relative to the plan, or "None"]

Next Steps:
1. Verify the feature works as expected
2. Commit with: git add [files] && git commit -m "feat: [description]"
3. Run /rpi-recap [FEATURE_NAME] to generate summary and clean up .rpi/
```

## Error Recovery

If you hit an obstacle:

1. Stop at the failure point — don't continue with broken code
2. Assess the error: review messages, logs, test output
3. Fix the issue before proceeding
4. Re-run quality gates to confirm the fix
5. Continue only after gates pass

## Critical Constraints

**Do:**

- Find repo root (directory containing `.git/`) before constructing `.rpi/` paths
- Read `PLAN_FILE` before starting — stop if missing
- Mark task status in plan file as you go (`[~]` → `[x]`)
- Respect task dependencies and phase boundaries
- Run quality gates after each parent task
- Stop and report on Major/Critical failures — never power through broken code
- Wait for user to verify before any commits

**Don't:**

- Skip or reorder tasks without user approval
- Mark a task `[x]` if quality gates failed
- Auto-commit changes
- Continue after a Critical failure
- Infer missing task context — ask the user if unclear
