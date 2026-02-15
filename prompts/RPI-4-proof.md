---
name: RPI-4-proof
description: "Auto-generate implementation summary and validate code changes against plan"
tags:
  - proof
  - validation
  - summary
arguments: []
meta:
  category: rpi-workflow
  allowed-tools: Glob, Grep, LS, Read, Write, Terminal, Git
---

# RPI Proof Phase

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI4️⃣

## You are here in the workflow

You have completed the **Implementation phase** and are now entering the **Proof phase**. This is where you validate that the code matches the plan, auto-generate a concise summary, and clean up temporary artifacts.

### Workflow Overview

**RPI workflow:**

- **Research → Plan**: Research findings → minimal plan (30-50 lines, temporary)
- **Plan → Implement**: Task list → working code (committed)
- **Implement → Proof** (current): Validation → summary (30-40 lines, committed) + cleanup

**Key principle**: Proof phase generates a **permanent, minimal summary** of "what changed and why" for human readers. Temporary scaffolding artifacts in `.rpi/[feature-name]/` are no longer needed and can be removed.

## Your Role

You are a **Technical Documenter** who creates concise, high-level summaries of implementation work. Your goal is to capture "what changed and why" in 30-40 lines for future reference, validate the implementation, and clean up temporary artifacts.

## Goal

Create a **minimal summary document** (30-40 lines) that captures what changed and why, validate the implementation against the plan, and clean up temporary artifacts.

**Output location**: `docs/rpi/[feature-name].md` (permanent, committed to repo)

**Cleanup**: Inform user that `.rpi/[feature-name]/` directory (temporary research and plan artifacts) can be removed

## Context

- **Plan file**: `.rpi/[feature-name]/plan.md` (contains task list and approach)
- **Research file**: `.rpi/[feature-name]/research.md` (contains original findings)
- **Repository root**: Current working directory
- **Implementation work**: On the current git branch

## Feature Name Discovery

If the user provides a feature name (e.g., `/rpi-4-proof user-auth`):

- Read `.rpi/user-auth/plan.md`

If no feature name is provided:

- Ask the user which feature to validate
- List available features in `.rpi/` directory

## Simple Validation Checklist

Before generating the summary, perform these quick checks:

- [ ] **Plan exists**: `.rpi/[feature-name]/plan.md` is readable
- [ ] **Code changes present**: `git diff` shows commits since plan was created
- [ ] **Tasks completed**: All tasks in the plan have corresponding code changes
- [ ] **Tests pass** (if applicable): Run test commands specified in plan verification steps
- [ ] **Quality checks pass** (if applicable): Pre-commit hooks, linting, type checking

**Note**: This is a lightweight validation, not a heavy proof-of-correctness exercise. Focus on "does it work?" and "does it match the plan?"

## Proof Process

### Step 1: Read Plan and Research

- Read `.rpi/[feature-name]/plan.md` to understand intended tasks
- Read `.rpi/[feature-name]/research.md` to understand original context
- Note the feature's complexity assessment and approach

### Step 2: Analyze Implementation

- Run `git log --stat -10` to see recent commits
- Run `git diff` to understand what changed
- Map commits to tasks in the plan
- Identify key files modified

### Step 3: Quick Validation

- Check that plan tasks have corresponding implementations
- Run any verification commands specified in the plan (tests, linting)
- Confirm basic functionality works

### Step 4: Generate Summary Document

Create `docs/rpi/[feature-name].md` with this **exact format**:

```markdown
# [FEATURE_NAME]

**Implemented**: [Date]
**Complexity**: [simple|medium|complex] (from research phase)

## What Changed

[3-5 bullet points describing what was implemented]
- Added [specific feature/component/file]
- Modified [existing functionality]
- Created [new functionality]

## Why

[2-3 sentences explaining the business/technical rationale for this feature]

## Key Files

- `[file_path]` - [what changed in this file]
- `[file_path]` - [what changed in this file]

## Implementation Notes

[2-4 bullet points of important technical decisions or patterns used]
- Followed [existing pattern] for [aspect]
- Used [approach/library] for [functionality]
- [Any gotchas or considerations for future maintainers]

## Verification

- [✓] Tests: [test command result or "N/A"]
- [✓] Quality: [linting/pre-commit result or "N/A"]
- [✓] Manual: [functionality confirmed or specific test performed]
```

#### Target: 30-40 lines total

**What to include:**

- High-level summary of changes
- Business/technical rationale
- Key files modified
- Important technical decisions
- Basic verification confirmation

**What to exclude:**

- Detailed code snippets or implementations
- Line-by-line change descriptions
- Verbose explanations
- Full git diff output
- Temporary research/plan details

### Step 5: Inform User About Temporary Artifacts

**IMPORTANT**: After successfully generating the summary document, inform the user that the temporary `.rpi/[feature-name]/` directory is no longer needed and can be removed.

This is a core principle of the RPI workflow - temporary scaffolding artifacts can be removed after implementation to achieve the 70% documentation reduction goal.

**Tell the user:**

> The temporary research and plan files in `.rpi/[feature-name]/` are no longer needed. You can remove them with:
>
> ```bash
> rm -rf .rpi/[feature-name]/
> ```

Let the user decide when to remove these files.

## Output Requirements

**Format:** Minimal markdown (30-40 lines)
**Path:** `docs/rpi/[feature-name].md` (permanent, committed to repo)
**Lifecycle:** Permanent - this is the only documentation artifact that persists

## Critical Constraints

**NEVER:**

- Create verbose documentation or detailed reports
- Include coverage matrices or heavy validation tables
- Write more than 40 lines in the summary document
- Skip informing the user about temporary artifacts that can be removed
- Include full code implementations or detailed git diffs
- Reference the temporary .rpi/ files in the permanent summary

**ALWAYS:**

- Read both `.rpi/[feature-name]/plan.md` and `.rpi/[feature-name]/research.md`
- Keep summary document minimal (30-40 lines)
- Focus on "what changed and why" not "how it was implemented"
- Save output to `docs/rpi/[feature-name].md`
- **Inform user that `.rpi/[feature-name]/` directory can be removed after summary is created**
- Run basic validation checks (tests, quality gates)
- Use the exact format specified above

## Directory Structure

**Before RPI-4-proof:**

```text
.rpi/
└── [feature-name]/
    ├── research.md  (temporary)
    └── plan.md      (temporary)

docs/rpi/
└── (empty or has other features)
```

**After RPI-4-proof:**

```text
.rpi/
└── [feature-name]/  (no longer needed, can be removed)
    ├── research.md
    └── plan.md

docs/rpi/
└── [feature-name].md  (permanent summary, 30-40 lines)
```

## Quality Checklist

Before completing the proof phase, verify:

- [ ] Read plan and research files
- [ ] Analyzed git commits and changes
- [ ] Ran verification checks from plan
- [ ] Generated summary document (30-40 lines)
- [ ] Saved to `docs/rpi/[feature-name].md`
- [ ] **Informed user that `.rpi/[feature-name]/` directory can be removed**
- [ ] Summary follows the exact format
- [ ] Summary is human-readable and focuses on "what/why"

## What Comes Next

Once the proof phase is complete:

1. **Summary created**: `docs/rpi/[feature-name].md` exists
2. **Ready for commit**: Working code + summary document ready to commit
3. **Workflow complete**: Research → Plan → Implement → Proof cycle finished

Instruct the user to:

- Review the summary document
- Commit the implementation and summary together
- Remove `.rpi/[feature-name]/` directory if desired (temporary artifacts no longer needed)
- Create a PR if needed (use `/create-pull-request` skill)

---

**Proof Completed:** [Date+Time]
**Summary Generated:** `docs/rpi/[feature-name].md`
**Note:** Temporary artifacts in `.rpi/[feature-name]/` can now be removed
