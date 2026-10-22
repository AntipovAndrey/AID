# AID Skills and Subagents

Reusable skills and subagent definitions for auditing documents, code
structure, and agent-authored branches. Six skills are slash commands that tell
Claude what to do; four subagents are roles that two of the skills run in
sequence. Every file is plain Markdown: use it as a Claude Code plugin, or paste
a skill or agent into any other model as a prompt.

## Install as a Claude Code plugin

```text
/plugin install aid --marketplace AntipovAndrey/AID
```

To try it without installing, run `claude --plugin-dir .` from the repository
root.

## Use

```text
/aid:audit-claims write a specification of the billing module from src/billing
/aid:audit-branch-slop feature/retry-queue against main
/aid:audit-comprehension README.md for a first-time contributor
/aid:audit-naming src/payments
/aid:decompose-module src/orders
/aid:spec-feature bulk export of invoices
```

Subagents can also be called directly: `@agent-aid:peirce`, `@agent-aid:socrates`,
`@agent-aid:hegel`, `@agent-aid:plato`. The `audit-claims` and
`audit-branch-slop` skills run them in the right order, passing each stage's
complete output to the next. The other four skills run without subagents.

## Index

| Category | Skill / Subagent Guide | Method |
| --- | --- | --- |
| Subagent Guides | [Logic Auditors](subagents/README.md#logic-auditors) — [Peirce](subagents/audit-claims/peirce.md), [Socrates](subagents/audit-claims/socrates.md), [Hegel](subagents/audit-claims/hegel.md) | Document claim reasoning, independently challenge claims, and resolve challenges against sources to produce verified documents. |
| Subagent Guides | [Incremental Changes Slop Detection](subagents/README.md#incremental-changes-slop-detection) — [Plato](subagents/plato.md) | Compare current code against the design a developer would have written knowing all requirements up front, to find slop: accidental complexity left by reactive fixes. |
| Skills | [`audit-claims`](skills/audit-claims/SKILL.md) | Run Peirce, Socrates, and Hegel in sequence, pass each stage's complete output to the next, and save every stage's report. |
| Skills | [`audit-branch-slop`](skills/audit-branch-slop/SKILL.md) | Prepare the branch diff and touched files, run Plato, and return the refactoring plan. |
| Skills | [`audit-comprehension`](skills/audit-comprehension/SKILL.md) | Compare fresh readers’ interpretations of prefixes growing clause by clause to locate comprehension breakdowns. |
| Skills | [`audit-naming`](skills/audit-naming/SKILL.md) | Compare names and contracts with statically traced behavior at module boundaries to uncover mismatches. |
| Skills | [`decompose-module`](skills/decompose-module/SKILL.md) | Trace calls from entry points, group use cases, and compare partitions by coupling and cohesion. |
| Skills | [`spec-feature`](skills/spec-feature/SKILL.md) | Ground a feature request in the running system, then grow one goal document by asking a single question at a time. |
