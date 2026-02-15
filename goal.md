The goal is simple: Create a better SDD workflow

## Problem Statement

### The Verbosity Problem

Current SDD generates excessive documentation relative to actual code changes:

**Real Example - Spec 08 (VS Code Insiders Support):**

- Implementation: 101 lines of actual code/tests
- SDD Documentation: 1,271 lines of specs, proofs, and validation
- **Ratio: 13:1 documentation-to-code**

**What's generated:**

- Spec document: 179 lines explaining a simple configuration addition
- Task breakdown: 95 lines for 4 straightforward tasks
- Proof artifacts: 494 lines documenting code that's self-evident
- Validation report: 377 lines verifying obvious functionality
- Questions document: 126 lines for a feature with clear requirements

**Why this is problematic:**

1. **Cognitive overhead**: Reading 1,271 lines to understand 101 lines of changes
2. **Maintenance burden**: 8,000+ lines of spec docs across the repository
3. **Diminishing returns**: Proof artifacts document what the code already shows
4. **Time waste**: Hours writing documentation that provides little incremental value
5. **Context switching**: Constant switching between implementation and documentation workflows

### The Sub-Agent Gap

Current SDD doesn't leverage sub-agents for:

- Research phase (exploring codebase, understanding patterns)
- Parallel task execution (independent units of work)
- Proof generation (can be automated from git diffs and test output)
- Validation (checking implementation against requirements)

### The Format Lock-In

Markdown-heavy format creates:

- Rigid structure that doesn't adapt to task complexity
- Copy-paste heavy workflows
- Information duplication across multiple documents
- Poor signal-to-noise ratio

## Solution: RPI (Research, Plan, Implement)

### Overview

RPI is a three-phase workflow that separates thinking from doing, reduces documentation overhead, and leverages sub-agents for complex tasks.

### Documentation Philosophy: Temporary vs. Permanent

**Two distinct types of documentation:**

1. **Permanent (Human-focused, committed to repo)**
   - High-level "what changed and why"
   - Written in markdown for human consumption
   - Stays in version control permanently
   - Purpose: Team understanding, code review, future reference
   - Format: Traditional markdown (README updates, change summaries)

2. **Temporary (AI-focused, deleted after implementation)**
   - Research findings (codebase exploration results)
   - Implementation plan (execution details)
   - Deleted once feature is implemented
   - Purpose: AI execution context during workflow only
   - Format: **Minimal markdown** (30-50 lines, optimized for AI parsing)
   - Human review: Brief approval only, not deep reading
   - Format chosen: Minimal markdown (30-50 lines) based on evaluation of 13 formats

**Key insight:** Research and Plan artifacts are **throwaway scaffolding** for AI execution. They don't need to be human-readable since they're temporary. This is why we optimize for AI parsing efficiency, not human readability.

### The Three Phases

**1. Research Phase** (Automated with sub-agents)

- Spawn parallel sub-agents to explore codebase
- Document existing patterns, architecture, and relevant files
- Output: `.rpi/[feature-name]/research.md` (30-50 lines, minimal markdown)
- Human review: Quick scan of findings
- **Lifecycle: TEMPORARY - deleted after implementation**

**2. Plan Phase** (AI-generated, human-approved)

- Create implementation plan with specific file paths and code snippets
- Define success criteria and verification steps
- Output: `.rpi/[feature-name]/plan.md` (30-50 lines, minimal markdown)
- Human review: Approve approach before implementation
- **Lifecycle: TEMPORARY - deleted after implementation**

**3. Implement Phase** (Automated execution)

- Execute plan step-by-step
- Run verification at each step
- Generate proof automatically from git diffs + test output
- Output:
  - Working code (committed)
  - Human-readable "what/why" summary (committed)
  - Research/Plan artifacts in `.rpi/[feature-name]/` (deleted)
- **Lifecycle: Code and summary are PERMANENT, scaffolding is deleted**

**Directory structure supports concurrent workflows:**

```text
.rpi/
├── user-auth/
│   ├── research.md  (temporary)
│   └── plan.md      (temporary)
├── payment-flow/
│   ├── research.md  (temporary)
│   └── plan.md      (temporary)
```

### How RPI Reduces Verbosity

**Temporary AI Artifacts (deleted after implementation):**

| Current SDD | RPI Workflow (Minimal Markdown) |
|-------------|----------------------------------|
| 179-line spec document | Research output (30-50 lines markdown) |
| 95-line task breakdown | Plan document with checkboxes (30-50 lines markdown) |
| 494 lines of proof artifacts | Auto-generated from git diff (temporary) |
| 377-line validation report | Test output + coverage report (temporary) |
| 126-line questions document | Clarifying questions in plan phase (inline) |
| **Total: 1,271 lines** | **Estimated: 60-100 lines (then deleted)** |

**Permanent Human Documentation (committed to repo):**

- High-level change summary: "Added VS Code Insiders support following existing VS Code pattern" (~10-20 lines)
- Updated README/docs with new agent listing (~20 lines)
- **Total permanent docs: ~30-40 lines** (vs. 1,271 lines in SDD)

### Sub-Agent Integration

- **Research**: Parallel exploration of multiple files/modules
- **Planning**: Sequential refinement of approach
- **Implementation**: Parallel execution of independent tasks
- **Validation**: Automated proof generation from artifacts

### When to Use RPI vs. Simple Implementation

- **Simple changes** (<100 lines, 1-2 files): Skip RPI, just implement
- **Medium complexity** (100-500 lines, 3-10 files): Use lightweight RPI (brief research, simple plan)
- **Complex changes** (>500 lines, 10+ files, architectural): Full RPI workflow

### Success Criteria

RPI is successful if:

1. Documentation reduced by 70%+ while maintaining quality
2. Human review happens at Research/Plan stages (not line-by-line)
3. Sub-agents handle 80%+ of exploration and proof generation
4. Context window stays under 40% (the "Smart Zone")
5. Code quality improves (fewer review comments, less rework)

### References

**External Resources:**

- [Research → Plan → Implement Pattern | Goose](https://block.github.io/goose/docs/tutorials/rpi/)
- [Context Engineering: RPI Framework | Fanpino](https://fanpino.com/en/blog/context-engineering-rpi-workflow-ai-coding/)
- [Introducing the RPI Strategy](https://patriciarobinson.com/blog/introducing-rpi-strategy/)
- [RPI Strategy GitHub Repository](https://github.com/patrob/rpi-strategy)
- [Claude Research Plan Implement Framework](https://github.com/brilliantconsultingdev/claude-research-plan-implement)

**Internal Documentation:**

- [Implementation Roadmap](roadmap.md) - 6-week plan to adopt RPI
- [Task Tracking](TASKS.md) - Planning progress and decisions

## Success Metrics and Evaluation Criteria

### Primary Metrics (Must Achieve)

#### 1. Documentation Efficiency

- Reduce documentation-to-code ratio from **13:1 to 2:1** or better
- Target: ~200 documentation lines per 100 lines of implementation
- Baseline: SDD = 1,271 lines per 101 lines of code

#### 2. Time Efficiency

- Reduce documentation overhead from **2-3 hours to <1 hour**
- Note: SDD time increases with deeper human code review
- RPI target: Front-load review in Research/Plan phases, minimal review in Implementation

#### 3. Context Window Health

- Stay below **40% context window usage** (the "Smart Zone")
- Track separately for Research, Plan, and Implement phases
- Poor context management degrades model performance significantly

### Quality Metrics (All Important)

**Code Quality Gates:**

- PR review comments: Track and minimize
- Rework iterations: Aim for ≤1 revision per feature
- Test coverage: Maintain project standards (≥90%)
- Bug escape rate: Issues found after merge (target: ≤1 per feature)

**Process Quality:**

- Human review happens at Research/Plan stages (not line-by-line code)
- Sub-agents handle ≥80% of exploration and proof generation
- Proof artifacts auto-generated from git diffs and test output

### Evaluation Framework

Test RPI on **3-5 real features** of varying complexity:

| Metric | SDD Baseline | RPI Target | Actual |
|--------|--------------|------------|--------|
| Doc-to-code ratio | 13:1 | 2:1 | ? |
| Documentation time | 2-3 hours | <1 hour | ? |
| Total cycle time | ? | 70% of SDD | ? |
| Context usage peak | ? | <40% | ? |
| PR review comments | ? | Reduce 50% | ? |
| Rework iterations | ? | ≤1 | ? |
| Bug escape rate | ? | ≤1 | ? |

### Success Definition

RPI is considered successful if:

1. ✅ **70%+ reduction** in documentation overhead while maintaining quality
2. ✅ **No increase** in bugs, rework, or quality issues
3. ✅ **Context stays healthy** (<40%) throughout workflow
4. ✅ **Developer confidence** high before merging (minimal line-by-line review needed)
5. ✅ **Sub-agents do the heavy lifting** (research, exploration, proof generation)

### Failure Indicators

Abandon or revise RPI if:

- ❌ Bug escape rate increases vs. SDD
- ❌ Rework iterations increase (more back-and-forth)
- ❌ Time savings don't materialize (still spending 2-3+ hours)
- ❌ Context window regularly exceeds 40% (model performance degrades)
- ❌ Human review still happening line-by-line (defeats the purpose)
