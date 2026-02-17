---
name: rpi-validate-research
description: "Plan phase: Break research into actionable tasks with verification steps"
tags:
  - planning
  - tasks
arguments: []
meta:
  category: rpi-workflow
  allowed-tools: Glob, Grep, LS, Read, Edit, MultiEdit, Write, WebFetch, WebSearch
---

# RPI Plan Phase

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI2️⃣

## You are here in the workflow

You have completed the **Research phase** and now need to break the implementation into actionable tasks. This is the critical planning step that bridges research findings to executable code.

### Workflow Overview

**RPI workflow:**

- **Research → Plan** (current): Research findings → minimal plan (max 50 lines, temporary)
- **Plan → Implement**: Task list → working code (committed)
- **Implement → Proof**: Execution → what/why summary (30-40 lines, committed)

**Key principle**: Plan artifacts are **temporary scaffolding** for AI execution, optimized for machine parsing, not human reading. They can be removed after implementation.

## Your Role

You are a **Technical Planner** responsible for breaking research findings into an actionable implementation plan. Your goal is to create a minimal plan that AI can execute systematically.

## Goal

Create a **minimal plan document** (max 50 lines) that defines:

- Tasks with clear dependencies
- Specific files to modify with code approach
- Verification steps to run after each task
- Success criteria

**Output location**: `.rpi/[feature-name]/plan.md` (temporary, can be removed after implementation)

## Planning Process

### Step 1: Read Research Findings

**Required input**: `.rpi/[feature-name]/research.md` must exist from previous Research phase.

If the user provides a feature name (e.g., `/rpi-2-plan user-auth`):

- Read `.rpi/user-auth/research.md`

If no feature name is provided:

- Ask the user which feature to plan
- List available features in `.rpi/` directory

The research document contains:

- Complexity assessment
- Existing patterns to follow
- Key files to modify
- Technical constraints
- Recommended approach

### Step 2: Break Down into Tasks

Create 3-7 tasks based on complexity:

- **Simple features**: 3-4 tasks
- **Medium features**: 4-6 tasks
- **Complex features**: 6-7 tasks (if more, consider splitting the feature)

Each task should:

- Be independently executable
- Have clear verification steps
- Specify exact files to modify
- Include brief code approach (not full implementation)

### Step 3: Define Dependencies

Identify which tasks must complete before others:

- **Sequential**: Task 2 requires Task 1 output
- **Parallel**: Tasks can be done simultaneously
- **Conditional**: Task only needed if previous task reveals issues

### Step 4: Add Verification Steps

For each task, specify how to verify completion:

- **Tests**: Which test command to run
- **Manual checks**: What to verify visually/functionally
- **Build checks**: Linting, type checking, compilation

### Step 5: Create Plan Document

Create `.rpi/[feature-name]/plan.md` using this **exact format**:

```markdown
# Plan: [FEATURE_NAME]

**From Research**: [1-2 sentence summary of research findings]
**Approach**: [1-2 sentence description of chosen approach]

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

#### Target: Plan can run longer than research (max 80 lines acceptable)

**What to include:**

- Brief summary of research context
- 3-7 parent tasks (1.0, 2.0, 3.0...) with clear actions
- 2-4 sub-tasks per parent task (1.1, 1.2, 1.3...)
- Specific file paths for each parent task
- Brief approach (not detailed code)
- Verification command or check
- Task dependencies
- Overall success criteria

**What to exclude:**

- Detailed code implementations
- Full file contents or large code blocks
- Verbose explanations
- Edge cases or error handling details
- Proof artifacts (auto-generated in Implement phase)

**Note:** Plan files can run longer than research files (max 80 lines acceptable) to accommodate sub-task breakdown.

### Step 6: Request Human Approval

After creating `.rpi/[feature-name]/plan.md`, show the user a summary and request approval:

1. Number of tasks and estimated complexity
2. Key dependencies between tasks
3. Overall implementation approach (1-2 sentences)

Ask: **"Does this plan capture the right sequence and approach?"**

Then immediately instruct:

**Important**: After showing the plan, tell them:

> Does this plan look good?
>
> **Next command:** `/RPI-3-implement [feature-name]`

**CRITICAL**: Always show the "Next command" line in your approval request. Do not wait for approval to suggest the next step.

## Output Requirements

**Format:** Minimal markdown (max 80 lines, can be longer than research)
**Path:** `.rpi/[feature-name]/plan.md`
**Lifecycle:** Temporary - can be removed after implementation

**Directory Structure:**

```text
.rpi/
├── user-auth/
│   ├── research.md  (from RPI-1)
│   └── plan.md      (created now)
├── payment-flow/
│   ├── research.md  (from RPI-1)
│   └── plan.md      (created now)
```

## Critical Constraints

**NEVER:**

- Create verbose documentation or detailed specs
- Include full code implementations
- Write more than 50 lines in the plan document
- Proceed to implementation without human approval
- Generate proof artifacts (auto-generated in Implement phase)
- Skip reading the research document

**ALWAYS:**

- Read `.rpi/[feature-name]/research.md` first
- Keep plan document focused but allow max 80 lines for sub-task breakdown
- Use parent task numbering (1.0, 2.0, 3.0) with sub-tasks (1.1, 1.2, 1.3)
- Specify exact file paths for each parent task
- Include verification steps for each parent task
- Define task dependencies clearly
- Save output to `.rpi/[feature-name]/plan.md` (supports concurrent workflows)
- Request human approval before proceeding
- Focus on actionable tasks, not documentation

## Quality Checklist

Before finalizing your plan, verify:

- [ ] Read research document from previous phase
- [ ] 3-7 parent tasks (1.0, 2.0, 3.0...) based on complexity assessment
- [ ] Each parent task has 2-4 sub-tasks (1.1, 1.2, 1.3...)
- [ ] Each parent task has specific file paths
- [ ] Each parent task has brief approach (1-2 lines)
- [ ] Each parent task has verification step
- [ ] Dependencies are clearly marked
- [ ] Success criteria are defined
- [ ] Format follows the exact structure (max 80 lines acceptable)
- [ ] Plan is actionable by AI in Implement phase

## What Comes Next

Once plan is approved by the user, they should run `/rpi-3-implement [feature-name]` to start the Implement phase, which will:

- Read `.rpi/[feature-name]/plan.md` for task list
- Execute tasks sequentially based on dependencies
- Verify after each task completion
- Auto-generate proof from git diffs + test output
- Create permanent "what/why" summary (30-40 lines)
- Inform user that temporary `.rpi/[feature-name]/` directory can be removed after proof phase
