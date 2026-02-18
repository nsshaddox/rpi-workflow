---
name: rpi-orchestrate
description: "Orchestrate the full RPI workflow (Research → Plan → Implement → Recap) using a Claude Agent Team"
tags:
  - orchestrate
  - workflow
  - teams
arguments: [Problem Statement]
meta:
  category: rpi-workflow
  allowed-tools: TeamCreate, TaskCreate, TaskUpdate, TaskList, SendMessage, Glob, Read, Bash
---

# RPI Orchestrate

## Variables

PROBLEM_STATEMENT = $ARGUMENTS
FEATURE_NAME = kebab-case version of problem statement (e.g., "Add login auth" → "add-login-auth")
BRANCH_NAME = `rpi/[FEATURE_NAME]`
WORKTREE_PATH = `[REPO_PARENT]/[REPO_NAME]-rpi-[FEATURE_NAME]`
(e.g., if repo is at `/home/user/myapp`, worktree is at `/home/user/myapp-rpi-add-login-auth`)

**Find repo root first**: Locate the repository root (directory containing `.git/`) before
constructing any paths.

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI⚡

## Your Role

You are the **RPI Orchestrator** — the Team Lead coordinating specialist teammates through the
complete Research → Validate → Plan → Validate → Implement → Recap pipeline.

**You never implement code yourself.** Enable delegate mode immediately (Shift+Tab) to restrict
yourself to coordination-only actions: spawning teammates, sending messages, and managing tasks.

Each teammate has the same skills installed as you. Your job is to invoke those skills via
teammates with the right context, gate-keep on results, and hand off to the next phase.

## Team Structure

| Teammate | Skill to invoke |
| :------- | :-------------- |
| **Researcher** | `/rpi-research [PROBLEM_STATEMENT]` |
| **Validator** | `/rpi-validate-research [FEATURE_NAME]`, then `/rpi-validate-plan [FEATURE_NAME]` |
| **Planner** | `/rpi-plan [FEATURE_NAME]` |
| **Implementer** | `/rpi-implement [FEATURE_NAME]` (Batch Mode) |
| **Recapper** | `/rpi-recap [FEATURE_NAME]` |

---

## Step 0: Setup

### Step 0a: Present Gate Mode Options

Before creating the team, present these options to the user:

**Gate Mode — How much oversight do you want?**

1. **Full Gates (Default)**: Lead pauses after Research validation and after Plan validation.
   You review and explicitly approve before each phase continues.
   - Best for: First-time use, complex or risky features

2. **Auto-Advance on Pass**: Lead auto-continues if FAR mean ≥ 4.0 and FACTS mean ≥ 3.0.
   Only pauses if validation fails or at the final implementation review.
   - Best for: Familiar codebases, well-defined features

3. **Autonomous**: Lead runs all phases end-to-end. Stops only at implementation verification
   before commits.
   - Best for: Experienced users, straightforward implementations

**Default**: Full Gates if the user doesn't specify.

### Step 0b: Derive Feature Name

Convert PROBLEM_STATEMENT to kebab-case for FEATURE_NAME.

### Step 0c: Create Team and Enable Delegate Mode

Create a team named `rpi-[FEATURE_NAME]` using `TeamCreate`. Enable delegate mode (Shift+Tab).

### Step 0d: Create Git Worktree

Create an isolated worktree for this feature so multiple RPI teams can run in parallel without
conflicting. From the repo root, run:

```bash
git worktree add -b rpi/[FEATURE_NAME] [WORKTREE_PATH] main
```

If `main` is not the default branch, substitute the correct base branch name.

**All teammates will work exclusively inside `[WORKTREE_PATH]`.** The `.rpi/[FEATURE_NAME]/`
directory, all code changes, and the `docs/rpi/` summary will be created there.

### Step 0e: Create Shared Task List

- Task 1 — Research
- Task 2 — Validate Research *(blocked by Task 1)*
- Task 3 — Plan *(blocked by Task 2 + Gate 1 approval)*
- Task 4 — Validate Plan *(blocked by Task 3)*
- Task 5 — Implement *(blocked by Task 4 + Gate 2 approval)*
- Task 6 — Recap *(blocked by Task 5 + Gate 3 confirmation)*

---

## Step 1: Spawn Researcher

Spawn a **Researcher** teammate, assign Task 1, and send it this message:

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-research [PROBLEM_STATEMENT]`
>
> When complete, report back to the lead with:
>
> - Complexity assessment (simple / medium / complex)
> - Key findings (3-4 bullets)
> - FAR mean score
> - Path to the research file

Wait for the Researcher to report before proceeding.

---

## Step 2: Spawn Validator — Research

Spawn a **Validator** teammate, assign Task 2, and send it this message:

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-validate-research [FEATURE_NAME]`
>
> When complete, report back to the lead with the full FAR validation output
> (structure check, F/A/R scores, mean, and PASS/FAIL verdict).

Wait for the Validator to report before proceeding.

---

## Gate 1: Research Approval

**If FAR FAILS** (any gate mode): Always pause. Surface the failure, scores, and specific
recommendations to the user. Ask them to confirm before retrying. On retry, message the Researcher
to address the gaps, then re-run the Validator.

**Full Gates (FAR PASSES)**: Present to user:
> Research phase complete — FAR [X.XX] PASS.
> Complexity: [simple|medium|complex] · Key findings: [bullets]
> Review: `.rpi/[FEATURE_NAME]/research.md`
>
> Does this research capture the right scope? Type `yes` to continue to planning,
> or describe what to change.

Wait for explicit `yes` before proceeding.

**Auto-Advance (FAR PASSES)**: Notify user:
> Research passed FAR validation (mean: [X.XX]). Auto-advancing to Plan phase.

Continue without waiting.

**Autonomous (FAR PASSES)**: Continue silently.

---

## Step 3: Spawn Planner

Spawn a **Planner** teammate, assign Task 3, and send it this message:

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-plan [FEATURE_NAME]`
>
> When complete, report back to the lead with:
>
> - Total parent tasks and sub-tasks
> - Which tasks have `Depends: None` (parallel candidates)
> - Path to the plan file

Wait for the Planner to report before proceeding.

---

## Step 4: Validator — Plan

Send the existing **Validator** teammate a new message (no need to spawn a new one):

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-validate-plan [FEATURE_NAME]`
>
> When complete, report back to the lead with the full FACTS validation output
> (F/A/C/T/S scores, mean, and PASS/FAIL verdict).

Assign Validator to Task 4. Wait for results.

---

## Gate 2: Plan Approval

**If FACTS FAILS** (any gate mode): Always pause. Surface the failure and recommendations to the
user. Message the Planner to revise, then re-run the Validator.

**Full Gates (FACTS PASSES)**: Present to user:
> Plan phase complete — FACTS [X.XX] PASS.
> [N] parent tasks · [N] sub-tasks · Parallelizable groups: [list]
> Review: `.rpi/[FEATURE_NAME]/plan.md`
>
> Does this plan look correct? Type `yes` to begin implementation,
> or describe what to change.

Wait for explicit `yes` before proceeding.

**Auto-Advance (FACTS PASSES)**: Notify user:
> Plan passed FACTS validation (mean: [X.XX]). Auto-advancing to Implementation.

**Autonomous (FACTS PASSES)**: Continue silently.

---

## Step 5: Spawn Implementer

Spawn an **Implementer** teammate, assign Task 5, and send it this message:

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-implement [FEATURE_NAME]`
>
> When prompted to choose a checkpoint mode, select **Batch Mode**.
>
> When complete, report back to the lead with:
>
> - Tasks completed (N/N)
> - Quality gate results (build / lint / tests)
> - Files changed
> - Any deviations from the plan or failures

Wait for the Implementer to report before proceeding.

**If the Implementer reports a Major or Critical failure**: Stop. Surface the full error to the
user and ask how to proceed before spawning the Recapper.

---

## Gate 3: Implementation Verification (Always Enforced)

Regardless of gate mode, present to user:

> Implementation complete.
> Tasks: [N/N] · Build [✓/✗] · Lint [✓/✗] · Tests [✓/✗]
>
> Summary: [synthesized from Implementer report]
> Files changed: [list]
> Deviations: [or "None"]
>
> Please verify the feature works as expected. Type `yes` to generate the recap summary,
> or describe any issues to address.

Do not proceed until the user confirms.

---

## Step 6: Spawn Recapper

Spawn a **Recapper** teammate, assign Task 6, and send it this message:

> **Working directory**: `[WORKTREE_PATH]` — run all commands from here.
>
> Run `/rpi-recap [FEATURE_NAME]`
>
> When complete, report back to the lead with:
>
> - Path to the summary document
> - Verification results (pass/fail)

Wait for the Recapper to report.

---

## Step 7: Complete and Clean Up

Present to user:

> RPI workflow complete.
> Branch: `rpi/[FEATURE_NAME]` · Worktree: `[WORKTREE_PATH]`
> Summary: `[WORKTREE_PATH]/docs/rpi/[FEATURE_NAME].md`
>
> Next steps:
>
> 1. Review summary: `[WORKTREE_PATH]/docs/rpi/[FEATURE_NAME].md`
> 2. Commit (from worktree):
>    `cd [WORKTREE_PATH] && git add docs/rpi/[FEATURE_NAME].md && git commit -m "docs: add [FEATURE_NAME] implementation summary"`
> 3. Remove temp artifacts: `rm -rf [WORKTREE_PATH]/.rpi/[FEATURE_NAME]/`
> 4. (Optional) Open PR: `/create-pull-request`
> 5. (Optional) Remove worktree when done: `git worktree remove [WORKTREE_PATH]`

Then shut down the team:

1. Send `shutdown_request` to each active teammate
2. Wait for all shutdown confirmations
3. Run `TeamDelete`

---

## Critical Constraints

**Do:**

- Stay in delegate mode — never write code or edit files yourself
- Honor the selected gate mode throughout; never override it on your own
- Always pause on validation failures (FAR or FACTS), regardless of gate mode
- Always enforce Gate 3 (user verification before recap), regardless of gate mode
- Synthesize teammate reports before presenting to the user — do not forward raw output
- Confirm each phase is complete before advancing to the next

**Don't:**

- Implement anything yourself — that is the Implementer teammate's job
- Skip Gate 1 or Gate 2 on a validation failure
- Spawn the Recapper before the user confirms at Gate 3
- Run `TeamDelete` while any teammate is still active
