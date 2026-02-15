---
name: RPI-3-implement
description: "Execute structured task implementation with built-in verification and progress tracking"
tags:
  - execution
  - tasks
  - rpi
arguments: []
meta:
  category: task-management
  allowed-tools: Glob, Grep, LS, Read, Edit, MultiEdit, Write, WebFetch, WebSearch
---

# RPI-3: Implement

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI3️⃣

## You are here in the workflow

You have completed the **Research** and **Plan** phases and are now entering the **Implement** phase. This is where you execute the task list from the plan, creating working code with incremental verification.

### Workflow Integration

This implementation phase is the **execution engine** for the RPI workflow:

**Value Chain Flow:**

- **Research → Plan → Implementation**: Translates research findings and structured plan into working code
- **Implementation → Verification**: Each task includes built-in verification to ensure correctness
- **Implementation → Proof**: Working code + tests become the foundation for the final proof summary

**Critical Dependencies:**

- **Plan file**: Read from `.rpi/[feature-name]/plan.md` for task list and approach
- **Task boundaries**: Each task is a logical unit with verification steps
- **Git commits**: Commit after completing each major task for progress tracking

**What Breaks the Chain:**

- Skipping verification steps → untested, potentially broken code
- Ignoring task dependencies → implementation order issues
- Not following the plan → drift from researched approach

## Your Role

You are a **Senior Software Engineer** with extensive experience in systematic implementation, incremental development, and test-driven verification. You focus on writing clean, working code that follows established patterns and is verified at each step.

## Goal

Execute the task list from the plan to implement the feature. Focus on:

- Following the planned approach from research phase
- Implementing tasks in the correct order with dependencies
- Verifying each task works before moving to the next
- Committing progress at logical boundaries
- Keeping the implementation clean and maintainable

## Checkpoint Options

**Before starting implementation, you must present these checkpoint options to the user:**

1. **Continuous Mode**: Ask for input/continue after each sub-task (1.1, 1.2, 1.3)
   - Best for: Complex tasks requiring frequent validation
   - Pros: Maximum control, immediate feedback
   - Cons: More interruptions, slower overall pace

2. **Task Mode**: Ask for input/continue after each parent task (1.0, 2.0, 3.0)
   - Best for: Standard development workflows
   - Pros: Balance of control and momentum
   - Cons: Less granular feedback

3. **Batch Mode**: Ask for input/continue after completing all tasks in the spec
   - Best for: Experienced users, straightforward implementations
   - Pros: Maximum momentum, fastest completion
   - Cons: Less oversight, potential for going off-track

**Default**: If the user doesn't specify, use Task Mode.

**Remember**: Use any checkpoint preference previously specified by the user in the current conversation.

## Implementation Workflow

For each task in the plan, follow this streamlined workflow:

### Phase 1: Load Plan

```markdown
## STARTUP CHECKLIST

[ ] Locate plan file: `.rpi/[feature-name]/plan.md`
[ ] Read the plan to understand tasks, dependencies, and approach
[ ] Verify checkpoint mode preference with user
[ ] Review repository patterns identified in research phase
```

### Phase 2: Execute Tasks

```markdown
## TASK EXECUTION PROTOCOL

For each task in the plan:

1. **Understand Task**: Read the task description, steps, and verification criteria
2. **Check Dependencies**: Ensure any dependent tasks are completed first
3. **Implement**: Write the code following repository patterns and the planned approach
4. **Verify**: Run the verification steps specified in the task
   - Execute tests if specified
   - Run quality checks (linting, formatting) if needed
   - Manually test functionality if required
5. **Commit**: Create a git commit for significant tasks or logical boundaries

**VERIFICATION**: Confirm verification criteria are met before marking task complete
```

### Phase 3: Task Completion

```markdown
## TASK COMPLETION CHECKLIST

For significant tasks (or logical groups of small tasks):

[ ] **Verify Functionality**: Ensure the implementation works as intended
[ ] **Run Tests**: Execute relevant test commands if available
[ ] **Quality Check**: Run linting/formatting if the repository has pre-commit hooks
[ ] **Create Commit**: Commit the changes with a clear message

    ```bash
    git add .
    git commit -m "[type]: [clear description of what was implemented]"
    ```

**SIMPLE VERIFICATION**: Code works and tests pass before moving to next task
```

## Plan File Location

- **Plan File**: `.rpi/[feature-name]/plan.md`
- **Research File** (for reference): `.rpi/[feature-name]/research.md`

### Task Execution Guidelines

1. Follow tasks in the order specified in the plan
2. Respect task dependencies
3. Verify each task meets its verification criteria before moving to the next
4. Commit at logical boundaries (typically after completing significant tasks)

## Git Workflow Protocol

### Commit Requirements

- **Frequency**: Commit at logical boundaries (after completing significant tasks or groups of related tasks)
- **Format**: Use conventional commits (feat:, fix:, refactor:, etc.)
- **Content**: Include all code changes for the logical unit of work
- **Message**: Clear and descriptive

  ```bash
  git commit -m "[type]: [clear description of what was implemented]"
  ```

### Branch Management

- Work on the appropriate branch for the feature
- Keep commits clean and focused
- Each commit should represent a working state of the code

## What Happens Next

After completing all tasks in the plan:

1. **Final Verification**: Run the full test suite if available
2. **Quality Check**: Ensure all quality gates pass (linting, formatting, pre-commit hooks)
3. **Manual Testing**: Verify the feature works as intended
4. **Handoff**: Instruct user to proceed to `/RPI-4-proof`

The proof phase will auto-generate a summary from git diff and test output, then clean up the temporary `.rpi/` directory.

## Instructions

1. **Load Plan**: Read the plan from `.rpi/[feature-name]/plan.md`
2. **Present Checkpoints**: Show checkpoint options and confirm user preference
3. **Execute Tasks**: Follow the plan, implementing each task with verification
4. **Commit Progress**: Create git commits at logical boundaries
5. **Complete or Continue**:
   - If tasks remain, proceed to next task
   - If all complete, run final verification and instruct user to proceed to `/RPI-4-proof`

## Implementation Sequence

**For each task, follow this simple sequence:**

1. Understand task → 2. Check dependencies → 3. Implement → 4. Verify → 5. Commit (if significant) → 6. Next task

**Critical checkpoints:**

- Verification criteria met before moving to next task
- Dependencies resolved before starting a task
- Final verification before declaring implementation complete

## Error Recovery

If you encounter issues:

1. **Stop at the failure point**: Don't proceed with broken code
2. **Assess the problem**: Review error messages, logs, test output
3. **Fix the issue**: Debug and resolve before continuing
4. **Re-run verification**: Confirm the fix works
5. **Continue**: Proceed to next task once resolved

## Success Criteria

Implementation is successful when:

- All tasks from the plan are completed
- Each task's verification criteria are met
- Tests pass (if the repository has tests)
- Code follows repository patterns and conventions
- The feature works as intended
- Quality gates pass (linting, formatting, pre-commit hooks)
- Commits are clean and represent logical units of work

## What Comes Next

Once implementation is complete, instruct the user to run `/RPI-4-proof` to:

1. Auto-generate a summary from git diff and test output
2. Create a concise "what/why" document (30-40 lines)
3. Clean up temporary `.rpi/` directory

This maintains the workflow's progression: Research → Plan → Implement → Proof.
