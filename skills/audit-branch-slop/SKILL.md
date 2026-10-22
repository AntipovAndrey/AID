---
name: audit-branch-slop
description: >-
  Audit a branch with several incremental commits, typically agent-authored, for
  accumulated accidental complexity: flags, shadow states, patch-over-patch
  control flow, smeared validation, swallowed errors. Runs the Plato subagent,
  which reconstructs the full requirement set from the branch history, designs
  the implementation a developer would have written knowing all requirements up
  front, and returns a refactoring plan. Use before merging a branch that grew by
  reaction to discovered corner cases. Not for reviewing a single commit.
---

# Audit Branch Slop

Later commits on a branch often compensate for an early design that did not know
about a requirement yet. Each patch is locally reasonable; the sum is not what
anyone would design from scratch. Plato compares the sum with the from-scratch
design and lists the difference as refactoring steps.

## 1. Gather inputs

Establish the branch under audit and its base. Collect:

- `branch_diff`: `git log --reverse -p <base>..<branch>`, so Plato sees commits
  in order with their messages. For large branches, use `git diff <base>..<branch>`
  plus `git log --reverse --format='%h %s%n%b' <base>..<branch>`.
- `base_branch_context`: the files the diff touches, as they are on the branch,
  so signatures and call sites are visible beyond the hunks. Prefer paths Plato
  can read over pasted content.

Do not pre-filter the diff by what looks suspicious. Plato must reconstruct the
whole requirement set, and a filtered diff hides requirements.

## 2. Run Plato

Launch the `plato` agent. The prompt contains, under these headings:

- `## Branch diff`: `branch_diff`, or the exact git command and the repository
  path if the agent can run it.
- `## Base branch context`: paths of the touched files.

Plato returns four sections: extracted requirements, slop audit by five
heuristics, clean-slate design, refactoring action plan.

## 3. Deliver

Give the user Plato's report unchanged. Add one block in front of it:

- Which of the five heuristics failed, by name.
- The number of refactoring steps.

Do not apply the refactoring plan in the same run. The plan is a proposal; the
user decides whether the branch is refactored before merge or the debt is
recorded.
