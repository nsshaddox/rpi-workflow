---
name: rpi-plan
description: "Plan phase: Break research into actionable tasks with verification steps"
tags:
  - planning
  - tasks
arguments: [CONTEXT]
meta:
  category: rpi-workflow
  allowed-tools: Glob, Grep, LS, Read, Edit, MultiEdit, Write, WebFetch, WebSearch
---

# RPI Plan Phase

## Variables

CONTEXT = $ARGUMENTS

### Resolve Research File

1. **Find repo root first**: Locate the repository root (directory containing `.git/`) before constructing any paths
2. **If ARGUMENTS provided**: Use as context to find related research file
3. **If ARGUMENTS empty**: Infer from the current git branch name
4. **If ARGUMENTS empty and branch is on main**: Look for `[REPO_ROOT]/.rpi/[FEATURE_NAME]/` with only a research file in the directory.

RESEARCH_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/research.md

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI2️⃣

## You are here in the workflow

You have completed the **Research phase** and now need to break the implementation into actionable tasks. This is the critical planning step that bridges research findings to executable code.

### Workflow Overview

**RPI workflow:**

- **Research** (completed): Explore codebase
- **Plan** (current): Break work into tasks
- **Implement**: Execute tasks
- **Recap**: Generate results

## Your Role

You are a **Technical Planner** responsible for breaking research findings into an actionable implementation plan. Your goal is to create a minimal plan that AI can execute systematically.

## Goal

Create a **minimal plan document** that defines:

- Tasks with clear dependencies
- Specific files to modify with code approach
- Verification steps to run after each task
- Success criteria

**Output location**: `[REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md` (temporary, can be removed after implementation)

## Planning Process

### Step 1: Resolve and Read the Research Document

1. Resolve FEATURE_NAME using the priority in Variables above
2. Find the repo root (directory containing `.git/`), then construct path: `[REPO_ROOT]/.rpi/[FEATURE_NAME]/research.md`
3. Read the file and verify it exists and is non-empty
4. If the file does not exist, tell the user and suggest running `/rpi-research` first

The research document contains:

- Complexity assessment
- Existing patterns to follow
- Key files to modify
- Technical constraints
- Recommended approach

Focus on identifying concrete, implementable items that can be translated into atomic tasks.

### Step 2: Analyze and Structure Tasks

Create 3-10 tasks based on complexity:

- **Simple features**: 3-4 tasks
- **Medium features**: 4-7 tasks
- **Complex features**: 7-10 tasks (if more, consider splitting the feature)

For each task, determine:

- **Dependencies**: Sequential (requires prior output), Parallel (can run simultaneously), or Conditional (only if needed)
- **Verification**: Tests to run, manual checks, or build checks (linting, type checking, compilation)
- **Files and approach**: Exact files to modify with a brief implementation note (not full code)

### Step 3: Create Plan Document

Create `[REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md` with:

- Brief summary of research context
- 3-10 parent tasks (1.0, 2.0, 3.0...) with clear actions
- 2-5 sub-tasks per parent task (1.1, 1.2, 1.3...)
- Specific file paths, brief approach, verification, and dependencies per task
- Overall success criteria

Use this **exact format**:

```markdown
# Implementation Plan: [Feature Name]

**Research**: [Link to research.md]
**Created**: [Date]

## Overview

[Brief summary of implementation]

### Key Decisions
- [Decision 1 from research]
- [Decision 2 from research]

### Approach
[Overall implementation strategy]

## Tasks

**Task 1.0**: [Action verb] [what to do]
- **Sub-tasks**:
  - [ ] 1.1: [Specific sub-task action]
  - [ ] 1.2: [Specific sub-task action]
  - [ ] 1.3: [Specific sub-task action]
- **Files**: `[file_path]` - [brief change description]
- **Approach**: [1-2 lines on how to implement]
- **Verify**: [command or manual check]
- **Depends**: None

**Task 2.0**: [Action verb] [what to do]
- **Sub-tasks**:
  - [ ] 2.1: [Specific sub-task action]
  - [ ] 2.2: [Specific sub-task action]
- **Files**: `[file_path]`, `[file_path]` - [brief change description]
- **Approach**: [1-2 lines on how to implement]
- **Verify**: [command or manual check]
- **Depends**: Task 1.0

**Task 3.0**: [Action verb] [what to do]
- **Sub-tasks**:
  - [ ] 3.1: [Specific sub-task action]
- **Files**: `[file_path]` - [brief change description]
- **Approach**: [1-2 lines on how to implement]
- **Verify**: [command or manual check]
- **Depends**: Task 1.0, Task 2.0

## Success Criteria

- [Criterion 1 - what must work]
- [Criterion 2 - what must pass]
- [Criterion 3 - what must be verified]
```

### Step 4: Summarize and Hand Off

After creating plan.md, provide a summary:

- Total tasks and sub-tasks created
- File path of created plan
- Brief overview of implementation approach
- Key insights from research analysis
- Reminder to run `/rpi-validate-plan plan.md` for formal FACTS validation

## Critical Constraints

**Do:**

- Find the repo root (directory containing `.git/`) before constructing any `.rpi/` paths
- Read `[REPO_ROOT]/.rpi/[FEATURE_NAME]/research.md` first; stop and prompt user if missing
- Use numbered tasks (1.0, 2.0...) with atomic sub-tasks (1.1, 1.2...)
- Specify exact file paths, verification steps, and dependencies per task
- Save output to `[REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md`
- Request human approval before implementation
- Keep tasks ≤10 per phase; split if larger

**Don't:**

- Write verbose docs, full code blocks, or detailed specs
