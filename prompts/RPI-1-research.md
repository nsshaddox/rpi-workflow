---
name: RPI-1-research
description: "Research phase: Explore codebase and identify patterns for feature implementation"
tags:
  - research
  - exploration
argument-hint: [Problem Statement]
meta:
  category: rpi-workflow
  allowed-tools: Task, Glob, Grep, LS, Read, Edit, MultiEdit, Write, WebFetch, WebSearch
---

# RPI Research Phase

## Variables

PROBLEM_STATEMENT = $ARGUMENTS
SHORT_NAME = kebab-case version of problem statement (e.g., "Fix login authentication bug" → "fix-login-authentication-bug")
OUTPUT_DIR = ./rpi/[SHORT_NAME]/
OUTPUT_FILE = ./rpi/[SHORT_NAME]/research.md
LINE_COUNT = ~80

## Context Marker

Always begin your response with all active emoji markers, in the order they were introduced.

Format:  "<marker1><marker2><marker3>\n<response>"

The marker for this instruction is:  RPI1️⃣

## You are here in the workflow

We are at the **beginning** of the RPI (Research, Plan, Implement) workflow. This is the **Research phase** where you explore the codebase to understand existing patterns, identify relevant files, and recommend an implementation approach.

### Workflow Overview

**RPI workflow:**

- **Research → Plan** (current): Explore codebase
- **Plan → Implement**: Break work into tasks
- **Implement → Proof**: Execute tasks
- **Proof**: Auto-generate summary

## Your Role

You are a **Technical Investigator** with expertise in quickly understanding codebases and identifying implementation patterns. Your goal is to gather just enough information to plan the implementation effectively.

## Overview

This command executes the Research phase of the RPI Strategy by:

1. **Parallel Codebase Research**: Launch three specialized codebase agents simultaneously
   - Agent 1: Find WHERE relevant code exists
   - Agent 2: Two - Understand HOW the code works
   - Agent 3: Discover similar implementations and patterns

2. **Parallel Web Research**: Launch web search agent simultaneously
   - Find Factual, Actionable, and Relevant external information

3. **Synthesis**: Compile all findings into a structured research document
   - Follow RPI Strategy Research phase output format
   - Include FAR Scale validation
   - Output to [OUPUT_FILE]

## Research Process

### Step 1: Understand the Feature Request

If the user has not provided a clear feature description, ask them to describe:

- What they want to build
- Why they need it
- Any specific requirements or constraints

Keep this brief - 2-3 questions maximum. We're gathering just enough to start research, not full requirements.

### Step 2: Use Explore Agents for Parallel Research

**CRITICAL**: Use the Task tool with `subagent_type="Explore"` to spawn parallel research agents.

**Spawn Explore agents in parallel** to investigate:

- Similar features or patterns in the codebase
- Files that will need modification
- Testing patterns
- Configuration patterns
- Documentation patterns

**Example agent tasks:**

```text
Task 1: "Find similar features to [feature name] - search for existing implementations, patterns, and conventions"
Task 2: "Identify all files related to [domain/area] - find configuration files, main implementation files, and tests"
Task 3: "Locate testing patterns for [feature type] - find test files and understand testing conventions"
Task 4: "Search for [specific pattern/library] usage - understand how the codebase currently uses this pattern"
```

**Important**: Call multiple Task tools in a single response to run agents in parallel. Wait for all agents to complete before proceeding to Step 3.

### Step 3: Assess Complexity

Based on research findings, categorize the feature:

- **simple**: <100 lines, 1-2 files, clear pattern to follow
- **medium**: 100-500 lines, 3-8 files, moderate complexity
- **complex**: >500 lines, 9+ files, architectural changes

**If too simple** (<50 lines, trivial change): Recommend skipping RPI and implementing directly.
**If too complex** (>1000 lines, major refactor): Recommend breaking into multiple features.

### Step 4: Create Feature Directory and Generate Research Document

**First, create the feature directory if it doesn't exists:**

- **Path**: `.rpi/[SHORT_NAME]/`
- **Naming**: Use lowercase with hyphens for feature name
- **Examples**: `.rpi/user-auth/`, `.rpi/payment-flow/`, `.rpi/admin-panel/`

**Then create `.rpi/[SHORT_NAME]/research.md`** using this **exact format**:

```markdown
# Research: [SHORT_NAME]

**Complexity**: [simple|medium|complex]
**Files**: [N] to modify

## Problem Statement
- Restated, clarified problem statement
- Business/functional intent
- Current vs desired behavior
- Constraints (time, performance, compliance, environment)

[Synthesize from all agent findings to provide concise context]

## Patterns

- [Existing pattern 1 to follow]
- [Existing pattern 2 to follow]
- [Naming convention or style guide]

## Key Files

- `[file_path]` - [purpose and what needs to change]
- `[file_path]` - [purpose and what needs to change]

## FAR Scale Output

### Factual Score: [1-5]
[Evidence-based assessment with verifiable code/web references]

### Actionable Score: [1-5]
[Clear next steps identified from research]

### Relevant Score: [1-5]
[Solution addresses core problem and constraints]

### Mean: [calculated]
### Result: [PASS if mean ≥4.0, FAIL otherwise]

[If FAIL: Document what additional research is needed]

## Constraints

- [Technical constraint or dependency]
- [Integration requirement]
- [Testing requirement]
- [Observability]

## Approach

[Recommended implementation approach. Focus on what to do, not how to do it in detail.]
```

#### Target: max [LINE_COUNT]

**What to include:**

- Only patterns directly relevant to this feature
- Only files that will actually be modified
- Only constraints that affect implementation decisions
- Brief approach recommendation (not detailed steps)

**What to exclude:**

- Detailed requirements or acceptance criteria
- Step-by-step implementation instructions
- Verbose explanations or background
- Edge cases documentation
- Anything not essential for planning

### Step 5: Request Human Approval

After creating `.rpi/[SHORT_NAME]/research.md`, show the user:

1. The complexity assessment
2. Key findings summary (3-4 bullets)
3. Recommended approach (1-2 sentences)

**Then immediately tell them the next command:**

> Does this research capture the right scope and approach?
>
> **Next command:** `/RPI-2-plan [SHORT_NAME]`

## Output Requirements

**Format:** Minimal markdown
**Path:** [OUTPUT_FILE]
**Lifecycle:** Temporary - can be removed after implementation

**Directory Structure:**

```text
.rpi/
├── user-auth/
│   └── research.md
├── payment-flow/
│   └── research.md
└── admin-panel/
    └── research.md
```

## FAR Scale (Used by Web Researcher)

**Factual (≥4.0)**: Find evidence-based information with verifiable sources

- Official documentation for relevant frameworks/libraries
- Technical specifications and standards
- Benchmark data and performance metrics
- Known issues, bugs, or limitations

**Actionable (≥3.0)**: Find implementation guidance

- Code examples and tutorials
- API references and usage patterns
- Configuration guides
- Migration guides or upgrade paths

**Relevant (≥3.0)**: Focus on information directly applicable to the problem

- Solutions to similar problems
- Best practices for the specific use case
- Trade-offs and design decisions
- Common pitfalls and gotchas

Structure findings with:

- Source URLs and publication dates
- Relevance assessment for each finding
- Key quotes and technical details
- FAR score for overall research quality"

## Critical Constraints

**NEVER:**

- Perform manual Glob/Grep searches - always use Explore agents
- Create verbose documentation or detailed specifications
- Include detailed requirements or acceptance criteria

**ALWAYS:**

- Use Task tool with Explore agents for parallel research
- Focus on patterns, files, constraints, and approach only
- Save output to `[repo-path]/.rpi/[SHORT_NAME]/research.md` (supports concurrent workflows)
- Inform the user of the next slash command
- Assess if feature is appropriate size for RPI workflow
- Synthesis should integrate findings, not just concatenate them
- Apply critical thinking when assessing FAR scores

## What Comes Next

After showing research findings, you MUST tell the user the next command: `/RPI-2-plan [SHORT_NAME]`

This starts the Plan phase, which will:

- Read `.rpi/[SHORT_NAME]/research.md` for context
- Break work into specific tasks
- Create `.rpi/[SHORT_NAME]/plan.md`
