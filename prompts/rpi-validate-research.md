---
name: rpi-validate-research
description: "Validate research document against FAR rubric before proceeding to Plan phase"
tags:
  - planning
  - tasks
arguments: []
meta:
  category: rpi-workflow
  allowed-tools: Glob, Grep, LS, Read, Edit, MultiEdit, Write, WebFetch, WebSearch
---

# RPI Validate Research

## Variables

CONTEXT = $ARGUMENTS

### Resolve Research File

1. **If ARGUMENTS provided**: Use as context to find related research file
2. **If ARGUMENTS empty**: Infer from the current git branch name
3. **IF ARGUMENTS empty and branch is on main** Look for `./rpi/[SHORT_NAME]/` with only a research file in the directory.

RESEARCH_FILE = ./rpi/[SHORT_NAME]/research.md

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI✅

## You are here in the workflow

We are **between Research and Plan** in the RPI workflow. This validation gate ensures the research document is strong enough to build a plan on.

### Workflow Overview

**RPI workflow:**

- **Research** (completed): Explore codebase
- **Validate Research** (current): Quality check before planning
- **Plan → Implement**: Break work into tasks
- **Implement → Proof**: Execute tasks

## Your Role

You are a **Research Validator** who objectively evaluates research quality. Your goal is to catch weak research before it produces a weak plan. Be rigorous but constructive.

## Validation Process

### Step 1: Resolve and Read the Research Document

1. Resolve SHORT_NAME using the priority in Variables above
2. Construct path: `./rpi/[SHORT_NAME]/research.md`
3. Read the file and verify it exists and is non-empty
4. If the file does not exist, tell the user and suggest running `/rpi-research` first

### Step 2: Check Structural Completeness

Verify these required sections are present and non-trivial:

- [ ] **Problem Statement** — restated problem with business intent and constraints
- [ ] **Patterns** — existing codebase patterns identified
- [ ] **Key Files** — specific file paths with purpose annotations
- [ ] **FAR Scale Output** — all three scores with numeric values and computed mean
- [ ] **Constraints** — technical constraints and dependencies
- [ ] **Testing Strategy** — unit, integration, observability, and risk areas
- [ ] **Assumptions** — enumerated, falsifiable statements
- [ ] **Out of Scope** — explicit exclusions defined
- [ ] **Approach** — recommended implementation direction

Mark each as present or missing.

### Step 3: Score FAR Dimensions

Using the research document's own FAR scores as a starting point, independently verify each:

**Factual (F)** — "Is this evidenced in the code/system?"

- Look for: code references, file paths that exist, concrete patterns, verifiable claims
- Threshold: **must be >= 4**

**Actionable (A)** — "Can I move this forward into a plan now?"

- Look for: concrete approach, identified files, clear constraints, enough context to plan
- Threshold: **must be >= 3**

**Relevant (R)** — "Does it address the actual problem?"

- Look for: problem alignment, scope boundaries, constraints that matter
- Threshold: **must be >= 3**

**Mean** = (F + A + R) / 3 — calculated to 2 decimal places.

### Step 4: Validate Assumptions and Scope

- Every assumption must be falsifiable (can be proven wrong)
- Every assumption must have a validation step or explicit deferral note
- Out of Scope section must define clear boundaries
- No assumption should contradict the stated constraints

### Step 5: Determine Result

**PASS** if ALL criteria met:

- F >= 4, A >= 3, R >= 3
- Mean >= 4.00
- All required sections present
- Assumptions are falsifiable with validation steps

**FAIL** if ANY criterion not met.

## Output Format

Return your evaluation in this format:

```text
=== FAR SCALE VALIDATION ===

STRUCTURE: [N/9] sections present
[List any missing sections]

SCORES:
F: [score]  A: [score]  R: [score]  Mean: [X.XX]  --> [PASS/FAIL]

ANALYSIS:

Factual (F = [score]):
[Justify with specific evidence from the document]

Actionable (A = [score]):
[Justify with specific evidence from the document]

Relevant (R = [score]):
[Justify with specific evidence from the document]

Assumptions: [VALID/ISSUES]
[Note any non-falsifiable or unverifiable assumptions]

[If PASS:]
VERDICT: Research meets FAR criteria. Ready for Plan phase.
Next command: /rpi-plan [SHORT_NAME]

[If FAIL:]
VERDICT: Research does not meet FAR criteria.

SEVERITY:
- Minor (Mean 3.5-3.9): Targeted improvements needed, quick iteration
- Major (Mean 2.0-3.4): Restart research with clearer focus
- Critical (Mean <2.0): Problem may be ill-defined or out of scope

RECOMMENDATIONS:
- [Specific gap and what to add/fix]
- [Specific gap and what to add/fix]

Next step: Address recommendations and re-run /rpi-research
```

## Critical Constraints

- Base scores strictly on evidence present in the document — do not infer
- Cite specific sections or gaps when justifying scores
- Do not create or modify any files — output evaluation to the user only
- If the research document self-scored FAR and you disagree, explain the discrepancy
- Be constructive on failures — provide actionable path to improvement
