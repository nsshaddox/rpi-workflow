# RPI Implementation - Task List

**Created**: 2026-02-14
**Updated**: 2026-02-14
**Project**: RPI (Research, Plan, Implement) workflow implementation
**Purpose**: Track progress on defining, documenting, and implementing the RPI approach

## Task Overview

### Planning Phase (Completed ✅)
| # | Status | Task |
|---|--------|------|
| 1 | ✅ Completed | Refine problem statement with specific examples and quantification |
| 2 | ✅ Completed | Define and elaborate on RPI solution approach |
| 3 | ✅ Completed | Define success metrics and evaluation criteria |
| 4 | ✅ Completed | Document key research questions to explore |
| 5 | ✅ Completed | Create actionable next steps and implementation roadmap |

### Implementation Phase
| # | Status | Task | Phase |
|---|--------|------|-------|
| 6 | ✅ Completed | Fork spec-driven-workflow repository | Phase 1 |
| 7 | ✅ Completed | Rename skill files (SDD → RPI) | Phase 1 |
| 8 | ✅ Completed | Update documentation (README, goal.md, roadmap.md, TASKS.md) | Phase 1 |
| 9 | ✅ Completed | Commit initial RPI fork and documentation | Phase 1 |
| 10 | ✅ Completed | Push to remote repository | Phase 1 |
| 11 | ✅ Completed | Modify RPI-1-research.md prompt instructions | Phase 2 |
| 12 | 📋 Pending | Modify RPI-2-plan.md prompt instructions | Phase 2 |
| 13 | 📋 Pending | Modify RPI-3-implement.md prompt instructions | Phase 2 |
| 14 | 📋 Pending | Modify RPI-4-proof.md prompt instructions | Phase 2 |

---

## Task #1: Refine problem statement with specific examples and quantification ✅

**Status**: Completed
**Owner**: N/A

### Description
Expand the problem statement in goals.md to include:
- Specific examples of verbosity (e.g., line counts, file sizes)
- Quantifiable pain points (time spent, cognitive load)
- Real-world scenarios where current SDD falls short
- User feedback or observations about excessive proof requirements
- Concrete examples of missed sub-agent opportunities

### Outcome
- Added concrete example from Spec 08 (VS Code Insiders)
- Documented 13:1 documentation-to-code ratio
- Broke down where 1,271 lines of documentation came from (spec: 179, tasks: 95, proofs: 494, validation: 377, questions: 126)
- Identified specific problems: cognitive overhead, maintenance burden, diminishing returns, time waste, context switching
- Documented sub-agent gaps and format lock-in issues

---

## Task #2: Define and elaborate on RPI solution approach ✅

**Status**: Completed
**Owner**: N/A

### Description
Flesh out the RPI (Research, Plan, Implement) concept:
- Define each phase clearly (Research, Plan, Implement)
- Explain how RPI differs from current SDD workflow
- Describe how RPI reduces verbosity while maintaining quality
- Detail how RPI leverages sub-agents more effectively
- Provide examples of RPI workflow in practice

### Outcome
- Researched RPI from multiple sources (Goose, Fanpino blog, GitHub implementations)
- Defined three phases with clear responsibilities
- Established documentation philosophy: **Temporary vs. Permanent**
  - Permanent (human-focused, committed): High-level "what/why" in markdown (~30-40 lines)
  - Temporary (AI-focused, deleted): Research/Plan artifacts in optimized format (~100-200 lines, then deleted)
- Created verbosity comparison table showing reduction from 1,271 to 100-200 temporary + 30-40 permanent lines
- Documented sub-agent integration strategy
- Defined when to use RPI vs. simple implementation
- Added references to source materials

**Key Insight**: Research and Plan artifacts are **throwaway scaffolding** for AI execution, optimized for machine parsing, not human reading.

---

## Task #3: Define success metrics and evaluation criteria ✅

**Status**: Completed
**Owner**: N/A

### Description
Establish clear success criteria for the improved workflow:
- Target reduction in documentation verbosity (e.g., "reduce from 1000 lines to X lines")
- Quality benchmarks (code correctness, completeness)
- Time/efficiency metrics (developer time saved)
- Proof adequacy thresholds (minimum vs sufficient)
- User satisfaction indicators
- Comparison framework between old SDD and new RPI

### Outcome
Added comprehensive success metrics section to goals.md:

**Primary Metrics:**
1. Documentation efficiency: 13:1 → 2:1 ratio
2. Time efficiency: 2-3 hours → <1 hour
3. Context window health: Stay below 40% (the "Smart Zone")

**Quality Metrics:**
- All important: PR comments, rework iterations, test coverage, bug escape rate
- Process quality: Human review at Research/Plan stages, sub-agents handle 80%+ of exploration

**Evaluation Framework:**
- Test on 3-5 real features of varying complexity
- Comparison table with baseline and target metrics

**Success Definition:**
- 70%+ documentation reduction
- No increase in bugs/rework
- Context stays healthy
- High developer confidence
- Sub-agents do heavy lifting

**Failure Indicators:**
- Bug escape rate increases
- More rework needed
- Time savings don't materialize
- Context exceeds 40%
- Line-by-line review still happening

---

## Task #4: Document key research questions to explore ✅

**Status**: Completed
**Owner**: N/A

### Description
Identify and document specific research questions that need answers:
- What is the optimal level of proof/documentation?
- Which parts of current SDD provide most value?
- How can sub-agents be integrated effectively?
- What alternative formats to markdown could work better?
- What are the technical constraints and requirements?
- What existing workflows or methodologies can we learn from?

### Outcome
**Format Analysis Completed:**
- Created 13 format examples (removed CSV as incomplete, added 5 AI-optimized formats)
- Conducted parallel subagent testing with identical parsing tasks
- Collected metrics: token usage, parsing time, difficulty ratings, completeness
- **Result: Minimal Markdown wins** (36 lines, 13,705 tokens, 13,027ms, 2/10 difficulty)

**Key Findings:**
1. **Token overhead is fixed**: All formats used ~13.7k tokens (±4%), suggesting cognitive overhead is format-independent
2. **Parsing speed matters**: Minimal markdown was 72% faster than full markdown
3. **Sweet spot: 30-50 lines**: Balance of conciseness and parseability
4. **Human readability valuable**: Markdown allows debugging/validation without losing AI efficiency

**Deliverables:**
- Format analysis completed: 13 formats evaluated
- Updated goal.md: Specifies minimal markdown as chosen format
- Recommendation: Use minimal markdown (30-50 lines) for temporary RPI artifacts
- Rationale: Best balance of parsing speed, token efficiency, and human readability

**Research Questions Answered:**
- ✅ Optimal format: Minimal markdown (best performance + human readability)
- ✅ Documentation level: 30-50 lines per phase (temporary), 30-40 lines permanent
- ✅ Sub-agent integration: Research/validation phases, parallel exploration
- ✅ Format optimization: Parsing speed > token count (fixed overhead hypothesis)

---

## Task #5: Create actionable next steps and implementation roadmap ✅

**Status**: Completed
**Owner**: N/A

### Description
Define concrete next steps for moving the project forward:
- Prototype or proof-of-concept plans
- Experiments to run (e.g., test RPI on small features)
- Tools/infrastructure needed
- Timeline or phases for exploration
- Decision points and milestones
- Who/what resources are needed

### Outcome

**Comprehensive 8-week roadmap created**: See [roadmap.md](roadmap.md)

**Four-phase approach**:
1. **Phase 1: Fork & Setup** (Week 1) - Fork repo, rename skills, document output formats
2. **Phase 2: Modify Prompts** (Weeks 2-4) - Adapt prompt instructions for RPI workflow
3. **Phase 3: Validation** (Week 5) - Test on 3 real features, collect metrics
4. **Phase 4: Rollout** (Week 6) - Document, train, adopt

**Key deliverables defined**:
- Modified prompt files: `RPI-1-research.md`, `RPI-2-plan.md`, `RPI-3-implement.md`, `RPI-4-proof.md`
- Output format examples: Minimal markdown for Research/Plan/Summary (30-50 lines)
- Validation metrics: Track doc-to-code ratio, time, context, quality
- Documentation: User guide, examples, best practices

**Decision points established**:
- Milestone 1 (Week 2): Does manual RPI meet targets?
- Milestone 2 (Week 4): Are tools functional and usable?
- Milestone 3 (Week 6): Does RPI meet success criteria?
- Milestone 4 (Week 8): Is team adopting successfully?

**Resources estimated**:
- ~48 hours total effort over 6 weeks
- Prompt modifications: ~20 hours (modifying 4 prompt files)
- Testing/validation: ~12 hours (3 features)
- Documentation/training: ~8 hours

**Next immediate action**: Select test feature for Phase 1 manual prototype

---

## Notes and Context

### Key Decisions Made
1. **Documentation split**: Permanent human-readable (committed) vs. Temporary AI-readable (deleted)
2. **Format selection**: Minimal markdown (30-50 lines) for temporary artifacts - best performance + human readability
3. **Format testing methodology**: Parallel subagent testing with identical tasks, measured tokens/time/difficulty
4. **Success criteria**: 70% documentation reduction without quality loss
5. **Time baseline**: Current SDD takes 2-3 hours for documentation
6. **Token overhead hypothesis**: AI cognitive load is ~13.7k tokens regardless of format (±4%)

### Files Created/Modified (Phase 1 ✅)
- `goal.md` - Main goals document with problem, solution, metrics
- `roadmap.md` - 6-week implementation roadmap
- `TASKS.md` - Task tracking and progress
- `README.md` - Updated with RPI branding
- `prompts/RPI-*.md` - Renamed from SDD-* (4 files, content unchanged)

### References
- [Goose RPI Tutorial](https://block.github.io/goose/docs/tutorials/rpi/)
- [Context Engineering Blog](https://fanpino.com/en/blog/context-engineering-rpi-workflow-ai-coding/)
- [RPI Strategy by Patrick Robinson](https://patriciarobinson.com/blog/introducing-rpi-strategy/)
- [GitHub RPI Strategy Repo](https://github.com/patrob/rpi-strategy)
- [Claude Research Plan Implement Framework](https://github.com/brilliantconsultingdev/claude-research-plan-implement)

---

## How to Resume

When resuming work in a new context window:

1. **Read this file** to understand current progress
2. **Read `goal.md`** for the complete RPI vision and success criteria
3. **Read `roadmap.md`** for the 6-week implementation plan
4. **Phase 1 complete** ✅ - Repository forked, documented, and committed
5. **Current status**: Ready for Phase 2 (modify prompt instructions)
6. **Next step**: Follow [roadmap.md](roadmap.md) Phase 2, Week 2 to modify RPI-1-research.md

## Current Session Summary (2026-02-14)

### What We Accomplished Today:
- ✅ Identified correct repository to fork (`spec-driven-workflow`, not `slash-command-manager`)
- ✅ Forked `spec-driven-workflow` → `rpi-workflow`
- ✅ Pulled latest changes from upstream
- ✅ Removed git remote (safe from accidental pushes)
- ✅ Renamed skill files: `SDD-*.md` → `RPI-*.md`
- ✅ Updated README with RPI branding and benefits
- ✅ Added comprehensive documentation (goal.md, roadmap.md, TASKS.md)
- ✅ Clarified approach: modifying prompts, not building tooling/templates
- ✅ Fixed all documentation misalignments
- ✅ Committed all changes locally

### What's Next:
- Create remote GitHub repository (optional, for sharing/installation)
- Push local commits to remote (when remote is ready)
- **Phase 2**: Begin modifying prompt instructions (Week 2 of roadmap)

## Quick Commands

```bash
# Navigate to project directory
cd /Users/nickshaddox/repos/FLYWHEEL/SDD-LIKE/rpi-workflow

# View goals document
cat goal.md

# View RPI skills
ls -la prompts/

# View this task list
cat TASKS.md
```
