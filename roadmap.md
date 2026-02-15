# RPI Implementation Roadmap

**Created**: 2026-02-14
**Updated**: 2026-02-14
**Status**: Ready to Build
**Timeline**: 6 weeks to full adoption

---

## Overview

This roadmap outlines the path from current SDD to RPI by **forking and adapting the existing SDD codebase**. The approach is: fork → modify → test → rollout.

**Success Criteria**: Achieve 70%+ documentation reduction while maintaining code quality (see [goals.md](goal.md) for full metrics)

**Key Insight**: Instead of building from scratch, we fork `spec-driven-workflow` and adapt the SDD skill prompts for RPI workflow. This gives us working skill templates with modifications only to format and lifecycle.

---

## Phase 1: Fork & Setup (Week 1)

**Goal**: Fork SDD repo and establish RPI foundation

### Day 1-2: Fork and Rename

**Actions**:

1. Fork `spec-driven-workflow` repository → `rpi-workflow`
2. Pull latest changes from upstream
3. Remove git remote to prevent accidental pushes to original
4. Rename skill files in `prompts/` directory:
   - `SDD-1-generate-spec.md` → `RPI-1-research.md`
   - `SDD-2-generate-task-list-from-spec.md` → `RPI-2-plan.md`
   - `SDD-3-manage-tasks.md` → `RPI-3-implement.md`
   - `SDD-4-validate-spec-implementation.md` → `RPI-4-proof.md`
5. Update README.md with RPI branding
6. Add RPI documentation (goal.md, roadmap.md, TASKS.md)

**Deliverables**:

- ✅ New repo: `rpi-workflow` (forked from spec-driven-workflow)
- ✅ Skills renamed in prompts/ directory
- ✅ Git remote removed (no accidental pushes)
- ✅ README updated with RPI branding
- ✅ RPI documentation added

### Day 3-5: Define Output Format Examples

**Document the minimal markdown format that prompts should instruct the AI to use**:

1. **Research output format** (AI creates `.rpi/[feature-name]/research.md`):

```markdown
# Research: [FEATURE_NAME]

**Complexity**: [simple|medium|complex]
**Files**: [N] to modify

## Patterns
- [pattern_1]
- [pattern_2]

## Key Files
- `[file_path]` - [purpose]

## Constraints
- [constraint]

## Approach
[recommended_approach]
```

1. **Plan output format** (AI creates `.rpi/[feature-name]/plan.md`):

```markdown
# Plan: [FEATURE_NAME]

**Lines**: [N] | **Time**: [Nh]

## Tasks

**1. [Task]** (`[file]`, ~[N] lines)
- [step]
- Verify: [criteria]

**2. [Task]** (`[file]`, ~[N] lines)
- Depends: #1
- [step]
- Verify: [criteria]

## Success
✅ [criteria]

## Risk
[risk] → [mitigation]
```

1. **Summary output format** (AI creates permanent summary doc):

```markdown
# [FEATURE_NAME]

## What
[brief_description]

## Why
[rationale]

## Testing
[verification_approach]
```

**Deliverables**:

- ✅ Output format examples documented (reference for prompt modifications)
- ✅ 30-50 line target defined for temporary artifacts
- ✅ Ready to modify prompt instructions

---

## Phase 2: Adapt RPI Skills (Weeks 2-4)

**Goal**: Modify SDD skills for RPI workflow

### Week 2: Adapt `RPI-1-research.md` prompt (from `SDD-1-generate-spec.md`)

**Modify the prompt instructions to:**

1. **Change output format**: Instruct AI to create minimal markdown (30-50 lines) instead of verbose spec
2. **Add Explore agent usage**: Instruct AI to use Explore agents for parallel codebase exploration
3. **Simplify focus**: Instruct AI to focus on patterns, files, constraints, approach only
4. **Change output location**: Instruct AI to save to `.rpi/[feature-name]/research.md` (supports concurrent workflows)
5. **Add human approval step**: Instruct AI to request approval before proceeding to plan phase

**What to remove from prompt instructions**:

- Detailed requirements sections
- Acceptance criteria sections
- Edge cases documentation
- Verbose explanations

**What to keep in prompt instructions**:

- File identification
- Pattern recognition
- Constraint analysis
- Recommended approach

**Target output**: 30-50 lines total in `.rpi/research.md`

**Testing**:

- Install modified skill via slash-command-manager
- Run `/rpi-1-research` on simple test case
- Verify output format is minimal (30-50 lines)
- Confirm Explore agents are used correctly
- Validate human approval flow works

**Deliverables**:

- ✅ `prompts/RPI-1-research.md` updated with new instructions
- ✅ AI generates minimal markdown output
- ✅ AI uses Explore agents as instructed
- ✅ Output saved to `.rpi/[feature-name]/research.md` (supports concurrent workflows)

### Week 3: Adapt `RPI-2-plan.md` prompt (from `SDD-2-generate-task-list-from-spec.md`)

**Modify the prompt instructions to:**

1. **Simplify task format**: Instruct AI to create concise task descriptions
2. **Change output format**: Instruct AI to create minimal markdown (30-50 lines total)
3. **Read from research**: Instruct AI to load `.rpi/[feature-name]/research.md` as context
4. **Streamline task details**: Instruct AI to include only steps and verification criteria
5. **Keep dependency tracking**: Task relationships still important for execution order

**What to remove from prompt instructions**:

- Long task descriptions
- Detailed acceptance criteria
- Verbose explanations

**What to keep in prompt instructions**:

- Task IDs and dependencies
- File paths to modify
- Verification criteria
- Risk/mitigation (1-2 lines)

**Target output**: 30-50 lines total in `.rpi/plan.md`

**Testing**:

- Install modified skill
- Run `/rpi-2-plan` after research phase
- Verify plan references research findings
- Confirm format is minimal (30-50 lines)
- Validate task dependencies are clear

**Deliverables**:

- ✅ `prompts/RPI-2-plan.md` updated with new instructions
- ✅ AI generates minimal markdown output
- ✅ AI reads and references `.rpi/[feature-name]/research.md`
- ✅ Output saved to `.rpi/[feature-name]/plan.md`

### Week 4: Adapt `RPI-3-implement.md` & `RPI-4-proof.md` prompts

**Modify `RPI-3-implement.md` prompt** (from `SDD-3-manage-tasks.md`):

- **Keep task execution logic**: Task-by-task implementation works well
- **Read from plan**: Instruct AI to load `.rpi/[feature-name]/plan.md`
- **Simplify verification**: Instruct AI to use less verbose proof requirements
- **Remove**: Instructions for detailed proof artifact generation during implementation

**Modify `RPI-4-proof.md` prompt** (from `SDD-4-validate-spec-implementation.md`):

- **Auto-generate proof**: Instruct AI to use `git diff` + test output
- **Create minimal summary**: Instruct AI to create 30-40 line summary focusing on "what/why"
- **Skip "how" details**: Code diffs show implementation details
- **Add cleanup instruction**: Instruct AI to delete `.rpi/` directory after summary is created

**Cleanup instructions to add to prompt**:

```text
After creating the summary document:
1. Commit the summary to the repository
2. Delete the temporary research and plan files:
   ```bash
   rm -rf .rpi/[feature-name]/
   ```

   Or to clean up all completed RPI workflows:

   ```bash
   rm -rf .rpi/
   ```

**Testing**:

- Install both modified skills
- Run full workflow: `/rpi-1-research` → `/rpi-2-plan` → `/rpi-3-implement` → `/rpi-4-proof`
- Verify `.rpi/` directory is deleted after proof
- Confirm summary is concise (30-40 lines) and human-focused
- Validate git diff is captured correctly

**Deliverables**:

- ✅ `prompts/RPI-3-implement.md` updated with simplified instructions
- ✅ `prompts/RPI-4-proof.md` updated with auto-generation and cleanup instructions
- ✅ AI deletes `.rpi/[feature-name]/` directory after summary creation
- ✅ Summary generation produces minimal, human-focused output

---

## Phase 3: Validation & Iteration (Week 5)

**Goal**: Test RPI on real features and iterate

### Test Features (Week 5)

**Run 3 features through complete RPI workflow**:

1. **Simple** (100-200 lines, 2-3 files) - e.g., Add new agent config
2. **Medium** (300-500 lines, 5-8 files) - e.g., Add new slash command
3. **Complex** (>500 lines, 10+ files) - e.g., Refactor core detection logic

**For each feature**:

- Execute full workflow: `/rpi-research` → `/rpi-plan` → `/rpi-implement` → `/rpi-proof`
- Collect metrics at each phase
- Document issues and friction points
- Iterate on skills between features
- Compare to SDD baseline

**Metrics to Track**:

| Metric | SDD Baseline | RPI Target | Feature 1 | Feature 2 | Feature 3 | Status |
|--------|--------------|------------|-----------|-----------|-----------|--------|
| Research lines | ~179 | 30-50 | ? | ? | ? | ? |
| Plan lines | ~95 | 30-50 | ? | ? | ? | ? |
| Permanent docs | ~1,271 | 30-40 | ? | ? | ? | ? |
| Total temporary docs | ~1,271 | 60-100 | ? | ? | ? | ? |
| Documentation time | 2-3h | <1h | ? | ? | ? | ? |
| Context peak | ? | <40% | ? | ? | ? | ? |
| Rework iterations | ? | ≤1 | ? | ? | ? | ? |

**Iteration Points**:

- After each feature, review metrics and friction
- Adjust skills, templates, or workflow as needed
- Re-test problem areas
- Document learnings

**Deliverables**:

- ✅ 3 features implemented with RPI
- ✅ Complete metrics collected
- ✅ Skills iterated and improved
- ✅ Pain points identified and addressed
- ✅ Best practices documented

---

## Phase 4: Rollout (Week 6)

**Goal**: Document and adopt RPI as primary workflow

### Documentation & Training (Week 6)

**Create Documentation**:

1. **RPI User Guide** (`docs/rpi-guide.md`)
   - When to use RPI vs simple implementation
   - How to use each skill (`/rpi-research`, `/rpi-plan`, etc.)
   - Best practices from validation phase
   - Common pitfalls and solutions

2. **RPI Examples** (`docs/rpi-examples/`)
   - Use the 3 validation features as examples
   - Simple, medium, complex feature walkthroughs
   - Show before/after metrics
   - Include actual research/plan/summary files

3. **Installation & Setup**
   - Update README with installation instructions
   - Document `.rpi/` directory structure
   - Explain temporary artifact lifecycle

**Adoption Activities**:

1. **Team Demo** (Day 1-2)
   - Walk through RPI workflow end-to-end
   - Show real examples from validation
   - Share metrics results
   - Answer questions

2. **Gradual Rollout** (Day 3-5)
   - RPI available as option alongside SDD
   - Early adopters start using
   - Collect feedback and iterate
   - Monitor metrics

3. **Set as Default** (After Week 6)
   - RPI becomes primary workflow
   - SDD remains available for exceptions
   - Continue monitoring adoption

**Deliverables**:

- ✅ Complete documentation (guide + examples)
- ✅ Team training completed
- ✅ Installation instructions updated
- ✅ RPI ready for production use
- ✅ Feedback loop established

---

## Decision Points & Milestones

### Milestone 1: End of Week 1 (Fork & Setup Complete)

**Decision**: Is RPI foundation ready?

- ✅ **GO**: Begin prompt modifications (Phase 2)
- ❌ **NO-GO**: Fix setup issues, resolve blockers

**Key Criteria**:

- Repo forked successfully
- Skills renamed (SDD → RPI)
- Output format examples documented
- `.gitignore` includes `.rpi/` directory

### Milestone 2: End of Week 4 (Prompts Modified)

**Decision**: Do all 4 RPI prompts instruct AI correctly?

- ✅ **GO**: Proceed to validation (Phase 3)
- ❌ **NO-GO**: Fix prompt instructions, test more

**Key Criteria**:

- AI generates minimal markdown when using `/rpi-1-research` (30-50 lines)
- AI reads research and creates plan with `/rpi-2-plan` (30-50 lines)
- AI executes tasks correctly with `/rpi-3-implement`
- AI auto-generates summary and cleans up with `/rpi-4-proof`
- Full workflow runs without errors

### Milestone 3: End of Week 5 (Validation Complete)

**Decision**: Does RPI meet success criteria across features?

- ✅ **GO**: Proceed to rollout (Phase 4)
- ⚠️ **ITERATE**: Fix issues, improve skills
- ❌ **NO-GO**: Major gaps, needs significant rework

**Success Criteria** (from goals.md):

1. ✅ 70%+ documentation reduction (60-100 lines vs 1,271)
2. ✅ Documentation time <1 hour (vs 2-3 hours)
3. ✅ No increase in bugs/rework
4. ✅ Context stays <40%
5. ✅ Positive qualitative feedback

### Milestone 4: End of Week 6 (Rollout Complete)

**Decision**: Is RPI ready for production use?

- ✅ **SUCCESS**: RPI is default, SDD is fallback
- ⚠️ **ISSUES**: Address blockers, extend timeline
- ❌ **FAILURE**: Significant problems, reassess

**Adoption Criteria**:

- Documentation complete and clear
- Team trained and comfortable
- No major blocking issues
- Metrics tracking established

---

## Resources Needed

### People

- **Developer(s)**: Modify RPI prompt files (estimated: 20 hours)
- **Pilot User(s)**: Test RPI workflow and provide feedback (estimated: 10-15 hours)
- **Reviewer(s)**: Review RPI artifacts and provide approval (ongoing)

### Tools & Infrastructure

- Claude Code CLI (already available)
- Explore agents (already available)
- Git (already available)
- slash-command-manager tool (already available)
- **New**: `.rpi/` temporary directory structure (created by AI during execution)
- **New**: Output format examples documented
- **Modified**: RPI prompt files with new instructions

### Time Commitment

- **Phase 1** (Week 1): ~8 hours (fork and setup)
- **Phase 2** (Weeks 2-4): ~20 hours (adapt skills)
- **Phase 3** (Week 5): ~12 hours (validation on 3 features)
- **Phase 4** (Week 6): ~8 hours (documentation & rollout)
- **Total**: ~48 hours over 6 weeks

---

## Risks & Mitigation

### Risk 1: RPI Doesn't Achieve Target Metrics

**Probability**: Medium
**Impact**: High

**Mitigation**:

- Start with manual prototype to validate approach
- Collect metrics early and often
- Have clear go/no-go decision points
- Keep SDD as fallback

**Contingency**: If metrics aren't met, iterate on workflow or revert to SDD

### Risk 2: Prompts Don't Instruct AI Correctly

**Probability**: Medium
**Impact**: Medium

**Mitigation**:

- Clear, unambiguous prompt instructions
- Test on multiple features during validation
- Iterate on prompt wording based on AI behavior
- Good documentation and examples

**Contingency**: Refine prompts, add more explicit instructions, provide more examples

### Risk 3: Team Resists New Workflow

**Probability**: Low
**Impact**: Medium

**Mitigation**:

- Show validation results (metrics improvement)
- Provide good examples and documentation
- Make adoption gradual with SDD fallback
- Listen to feedback and iterate

**Contingency**: Address concerns, improve tools, extend adoption timeline

### Risk 4: Complex Features Don't Fit RPI

**Probability**: Low
**Impact**: High

**Mitigation**:

- Test RPI on varying complexity during validation
- Build flexibility into workflow (skip phases, adapt format)
- Keep "when to use RPI vs simple" guidelines

**Contingency**: Define exceptions where SDD or custom approach is better

---

## Success Indicators

### Early Success Signs (Week 1)

- ✅ Fork completed without issues
- ✅ Skills renamed (SDD → RPI)
- ✅ Output format examples documented
- ✅ Ready to modify prompts

### Mid-Point Success Signs (Weeks 2-5)

- ✅ All 4 prompts modified with new instructions
- ✅ AI generates minimal markdown (30-50 lines) as instructed
- ✅ Validation features meet metrics targets
- ✅ Context usage stays below 40%
- ✅ Positive qualitative feedback

### Final Success Signs (Week 6)

- ✅ Documentation complete and clear
- ✅ Team trained and comfortable
- ✅ Metrics consistently better than SDD
- ✅ No blocking issues identified

---

## Failure Indicators

### Early Warning Signs

- ❌ Manual prototype doesn't meet metrics
- ❌ Workflow feels clunky or slow
- ❌ Context window regularly exceeds 40%
- ❌ Human review is too time-consuming

### Critical Failure Signs

- ❌ Bug escape rate increases
- ❌ Rework iterations increase
- ❌ Time savings don't materialize
- ❌ Team feedback is consistently negative
- ❌ Quality decreases

**Action if failure indicators appear**: Pause rollout, analyze root causes, iterate on approach, or revert to SDD

---

## Next Immediate Actions

**Start Phase 1 (Week 1) with:**

1. **Fork spec-driven-workflow Repository** (Day 1) ✅ COMPLETED

   ```bash
   cd /Users/nickshaddox/repos/FLYWHEEL/SDD-LIKE
   # Pull latest changes first
   git -C /path/to/spec-driven-workflow pull origin main
   # Copy to create fork
   cp -r /path/to/spec-driven-workflow rpi-workflow
   cd rpi-workflow
   # Remove remote to prevent accidental pushes
   git remote remove origin
   ```

1. **Update README** (Day 1) ✅ COMPLETED
   - Update title and description to reflect RPI workflow
   - Update installation commands to reference rpi-workflow
   - Highlight 70%+ documentation reduction benefit

1. **Rename Skill Files** (Day 1-2) ✅ COMPLETED

   ```bash
   cd prompts/
   mv SDD-1-generate-spec.md RPI-1-research.md
   mv SDD-2-generate-task-list-from-spec.md RPI-2-plan.md
   mv SDD-3-manage-tasks.md RPI-3-implement.md
   mv SDD-4-validate-spec-implementation.md RPI-4-proof.md
   ```

1. **Document Output Formats** (Day 2-3)
   - Document desired research output format (30-50 lines)
   - Document desired plan output format (30-50 lines)
   - Document desired summary output format (30-40 lines)
   - These serve as reference examples when modifying prompts

1. **Setup `.gitignore`** (Day 3-4)
   - Add `.rpi/` to `.gitignore` so temporary files aren't committed
   - Push rpi-workflow to your GitHub repository (optional, for testing installation)

1. **Test Current Workflow** (Day 4-5)
   - Install current RPI skills using slash-command-manager:

     ```bash
     uvx --from git+https://github.com/liatrio-labs/slash-command-manager \
       slash-man generate \
       --github-repo <your-org>/rpi-workflow \
       --github-branch main \
       --github-path prompts/
     ```

   - Run skills (they still have SDD behavior - that's expected)
   - Understand current SDD behavior before modifying prompts
   - Note: Modifications happen in Phase 2

**Ready for Phase 2**: Begin adapting `/rpi-research` skill

---

## Appendix: Output Format Examples

These are examples of what the AI should generate when following the modified prompt instructions. These serve as reference when writing prompt instructions.

### Research Output Format (30-50 lines)

```markdown
# Research: [Feature Name]

**Complexity**: [simple|medium|complex]
**Files to modify**: [N]

## Existing Patterns
[Key pattern 1]
[Key pattern 2]

## Files & Locations
- `file/path.ext` - [purpose]
- `file/path2.ext` - [purpose]

## Constraints
- [Constraint 1]
- [Constraint 2]

## Recommendations
[Approach recommendation]
```

### Plan Output Format (30-50 lines)

```markdown
# Plan: [Feature Name]

**Lines**: [N] | **Time**: [Nh]

## Tasks

**1. [Task Name]** (`file/path.ext`, ~[N] lines)
- [Step 1]
- [Step 2]
- Verify: [criteria]

**2. [Task Name]** (`file/path.ext`, ~[N] lines)
- Depends: #1
- [Step 1]
- Verify: [criteria]

## Success
✅ [Criteria 1] | ✅ [Criteria 2]

## Risk
[Risk description] → [Mitigation]
```

### Summary Output Format (30-40 lines)

```markdown
# [Feature Name]

## What Changed
[Brief description of what was added/modified/removed]

## Why
[Rationale for the change]

## How to Use
[Instructions for users, if applicable]

## Testing
[How this was verified]
```

---

## Conclusion

This roadmap provides a fast, structured path from SDD to RPI in just **6 weeks** by **forking and adapting the existing SDD codebase**. The approach prioritizes speed while maintaining quality with clear go/no-go decision points at each milestone.

**Key Success Factors**:

1. **Leverage existing code**: Fork SDD instead of building from scratch
2. **Test while building**: Validate each skill as it's adapted
3. **Measure everything**: Track metrics from Week 2 onward
4. **Iterate quickly**: Fix issues immediately during validation
5. **Document learnings**: Capture best practices for rollout

**Why This Approach Works**:

- ✅ Reuses proven prompt structure (skills, approval flows, task management)
- ✅ Faster than building from scratch (~48 hours vs ~55+ hours)
- ✅ Lower risk - modifying working prompts vs creating new ones
- ✅ Familiar patterns for team adoption
- ✅ Test-as-you-modify approach validates continuously

**Timeline Summary**:

- Week 1: Fork and setup
- Weeks 2-4: Adapt 4 RPI skills
- Week 5: Validate on 3 real features
- Week 6: Document and rollout

**Ready to begin**: Start with Phase 1, Week 1 - Fork `slash-command-manager-sdd` → `slash-command-manager-rpi`
