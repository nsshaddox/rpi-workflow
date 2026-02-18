---
name: rpi-validate-plan
description: Validate a plan against the FACTS rubric and return score, pass/fail status, and recommendations
tags:
  - planning
  - validate
arguments: [CONTEXT]
meta:
  category: rpi-workflow
  allowed-tools: Read
---

# RPI Validate Plan

## Variables

CONTEXT = $ARGUMENTS

### Resolve Plan File

1. **Find repo root first**: Locate the repository root (directory containing `.git/`) before constructing any paths
2. **If ARGUMENTS provided**: Use as context to find related plan file
3. **If ARGUMENTS empty**: Infer from the current git branch name
4. **If ARGUMENTS empty and branch is on main**: Look for `[REPO_ROOT]/.rpi/[FEATURE_NAME]/` with only a research and plan file in the directory. Verify with user before continuing.

PLAN_FILE = [REPO_ROOT]/.rpi/[FEATURE_NAME]/plan.md

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI✅

## You are here in the workflow

We are **between Plan and Implement** in the RPI workflow. This validation gate ensures the plan is sound enough to execute.

### Workflow Overview

**RPI workflow:**

- **Research** (completed): Explore codebase
- **Plan** (current): Break work into tasks
- **Implement**: Execute tasks
- **Proof**: Generate results

## Your Role

You are a **Plan Validator** who objectively evaluates plan quality against the FACTS criteria. Your goal is to catch weak plans before they produce broken implementations.

## Validate Plan Against FACTS Rubric

You are tasked with evaluating a technical implementation plan against the FACTS Scale rubric to determine if it meets quality standards for proceeding to the Implement phase.

## Your Task

1. **Read and Understand the Plan**: Review all tasks in the provided plan file to understand the implementation approach, task breakdown, and execution strategy.

2. **Apply the FACTS Rubric**: Use the following scoring criteria for each dimension (0-5):

   **Feasibility** — Resource Implementation Capability
   - 0: Requires non-existent technology or violates fundamental constraints
   - 1: Requires deep domain expertise, multiple senior developers, or extensive research
   - 2: Needs experienced developer with specific framework knowledge and significant investigation
   - 3: Junior developer can complete with mentoring and some research/documentation review
   - 4: Junior developer can complete independently with standard documentation
   - 5: Can be completed by copy-paste, configuration change, or single API call

   **Atomicity** — Single Responsibility Focus
   - 0: Spans multiple systems, requires weeks of work, affects dozens of files
   - 1: Single feature but touches many components, requires multiple PRs to review safely
   - 2: Clear feature boundary but involves several related changes across multiple files
   - 3: Single responsibility with minimal cross-cutting concerns, 2-5 file changes
   - 4: One clear objective, 1-3 file changes, single aspect of functionality
   - 5: Indivisible unit of work, single file change, one logical modification

   **Clarity** — Execution Order & Dependency Transparency
   - 0: No clear execution order, circular dependencies, conflicting requirements
   - 1: Dependencies exist but are unclear, multiple valid interpretation paths
   - 2: General order apparent but specific sequencing decisions left to implementer
   - 3: Dependencies identified, execution order determinable with minor investigation
   - 4: Clear prerequisite chain, unambiguous next step at each stage
   - 5: Perfect linear dependency chain, each task enables exactly the next one

   **Testability** — Completion Verification Capability
   - 0: No way to verify completion, purely subjective outcomes
   - 1: Requires extensive manual testing, no automated verification possible
   - 2: Completion visible through logs/UI but requires complex setup to verify
   - 3: Clear pass/fail criteria, can be tested but requires some manual steps
   - 4: Automated tests can validate completion, clear success metrics
   - 5: Unit tests, integration tests, or simple commands definitively prove completion

   **Size** — Task Scope Appropriateness
   - 0: Multiple systems, cross-team coordination, requires comprehensive project planning
   - 1: Single feature spanning many files, multiple complex interconnected changes
   - 2: Feature with several related changes, moderate cross-cutting concerns
   - 3: Focused task with clear boundaries, typical development work
   - 4: Straightforward implementation, minimal scope, well-defined change
   - 5: Atomic unit: single file edit, configuration change, or simple command

3. **Evaluate Each Task**: For each task in the plan, assign a score (0-5) for each FACTS dimension based on the rubric criteria.

4. **Calculate Mean Score**:
   - Calculate the arithmetic mean of all five dimensions (F, A, C, T, S)
   - Round to exactly 2 decimal places
   - Pass threshold: Mean >= 3.00

5. **Determine Pass/Fail Status**:
   - **PASS**: Mean score >= 3.00 AND all individual scores are reasonable
   - **FAIL**: Mean score < 3.00 OR any critical dimension fails (e.g., F < 3 indicates infeasible tasks)

6. **Provide Structured Output**:

### For PASS Status

```text
FACTS VALIDATION RESULT
=======================

Overall Assessment: PASS ✓

FACTS Scores:
F: [score]  A: [score]  C: [score]  T: [score]  S: [score]  Mean: [X.XX]  --> PASS

Summary:
[Brief explanation of why the plan passes, highlighting strengths]

The plan meets quality standards and is ready to proceed to the Implement phase.

Next Steps:
Run /rpi-implement to begin executing tasks.
```

### For FAIL Status

```text
FACTS VALIDATION RESULT
=======================

Overall Assessment: FAIL ✗

FACTS Scores:
F: [score]  A: [score]  C: [score]  T: [score]  S: [score]  Mean: [X.XX]  --> FAIL

Failure Classification:
[MINOR / MAJOR / CRITICAL based on mean score]
- Minor (2.8-2.9): Single iteration task refinement needed
- Major (2.0-2.7): Return to Research phase for problem decomposition
- Critical (<2.0): Leadership escalation required

Failure Reason:
[Explain why the plan failed - mean below 3.00 or specific dimension issues]

Recommendations to Improve:

1. **[Dimension Name]** (Current: [score], Target: >= 3)
   - Issue: [Specific problem identified]
   - Recommendation: [Concrete action to improve]

2. **[Dimension Name]** (Current: [score], Target: >= 3)
   - Issue: [Specific problem identified]
   - Recommendation: [Concrete action to improve]

[Continue for all failing dimensions]

Next Steps:
- **Minor Failure**: Refine task descriptions, adjust estimates, re-validate with /rpi-validate-plan
- **Major Failure**: Return to Research phase for missing context or problem decomposition
- **Critical Failure**: Escalate to technical lead, consider problem unfeasible with current approach
```

## Critical Constraints

**Do:**

- Find repo root before constructing any `.rpi/` paths
- Score based strictly on the rubric criteria, not personal preferences
- Cite specific tasks or sections from the plan when identifying issues
- Frame recommendations as actionable improvements
- Calculate mean to exactly 2 decimal places

**Don't:**

- Create or modify any files — return all output directly to the user
- Pass a plan with mean < 3.00 or any critical dimension below threshold
- Skip scoring individual tasks — evaluate each one against all five FACTS dimensions

## Failure Severity Guidelines

**Minor Failure (Mean 2.8-2.9):**

- Plan is close to passing
- Single iteration refinement likely sufficient
- Focus on task description clarity and scoping adjustments

**Major Failure (Mean 2.0-2.7):**

- Significant gaps in plan quality
- May need additional research or problem decomposition
- Return to Research phase for enhanced context

**Critical Failure (Mean <2.0):**

- Fundamental issues with feasibility or approach
- Escalate to technical leadership
- Consider problem unfeasible with current approach or resources

The FACTS Scale ensures that plans are executable, maintainable, and set up for successful implementation.
