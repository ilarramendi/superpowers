---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, the code, how to verify it works, and docs they might need to check. Give them the whole plan as bite-sized tasks. DRY. YAGNI. Frequent commits.

**Testing approach:** Write the implementation first. Add tests only when they earn their keep — complex logic, edge cases hard to verify manually, regression prevention for real bugs, public API contracts, security-sensitive code. See `superpowers:pragmatic-testing` for the decision framework. For each task, the plan should specify: write a test, verify manually, or both — and why.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** If working in an isolated worktree, it should have been created via the `superpowers:using-git-worktrees` skill at execution time.

**Save plans to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Parallelism (Optional)

If two or more tasks touch **completely disjoint files** with **no cross-dependencies**, mark them with the same `**Parallel group:**` label. The executor will dispatch the whole group simultaneously.

Most plans are sequential — only add this when it's obvious. When in doubt, leave it out.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Implement the function" - step
- "Run the verification command" - step
- "Inspect output / behavior" - step
- (If a test is warranted) "Write a focused test" - step
- (If a test is warranted) "Run the test and confirm it passes" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Parallel group:** A *(optional — omit if sequential)*

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- (Test, only if warranted): `tests/exact/path/to/test.py`

**Test decision:** [State explicitly: "Skip — trivial glue, verify by running script" OR "Write test — non-trivial branching logic, hard to verify manually" OR "Regression test — reproduces bug #123"]

- [ ] **Step 1: Implement**

```python
def function(input):
    # full implementation
    return result
```

- [ ] **Step 2: Verify manually**

Run: `python -m mymodule.cli --input sample.json`
Expected: prints `{"result": ...}`, exit code 0

- [ ] **Step 3 (only if test warranted): Write the test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 4 (only if test warranted): Run test, confirm pass**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add src/path/file.py tests/path/test.py
git commit -m "feat: add specific feature"
```
````

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, frequent commits
- Tests only when they earn their keep — see `superpowers:pragmatic-testing`

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `docs/superpowers/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Fresh subagent per task + two-stage review

**If Inline Execution chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:executing-plans
- Batch execution with checkpoints for review
