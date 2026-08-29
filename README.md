# AID Subagents

This repository contains reusable subagent definitions. Each Markdown file in
[`subagents/`](subagents/) defines an agent's role, inputs, instructions, and
output format.

## Logic Auditors

The current agents form a three-stage workflow for producing a non-code
deliverable whose important claims are traceable, challenged, and resolved.
Together, they produce a more trustworthy document than a bare agent typically
would: the workflow forces deeper research and prevents early mistakes from
becoming assumptions that silently support later conclusions.
They should be run in the following order:

1. **Peirce — Analyst** ([`subagents/peirce.md`](subagents/peirce.md))
   - Executes the user's task and creates the initial deliverable.
   - Records every significant claim in a Logic Log.
   - Classifies reasoning as deduction (`D`), induction (`I`), or abduction
     (`A`) and states the basis for each classification.
   - Inputs: `input_data`, `user_prompt`.

2. **Socrates — Auditor** ([`subagents/socrates.md`](subagents/socrates.md))
   - Audits every entry in Peirce's Logic Log for factual accuracy, logical
     soundness, and alignment with the original objective.
   - Applies reasoning-specific attacks and supplies a constructive alternative
     hypothesis for every challenged claim.
   - Assigns a risk rating and summarizes systemic issues.
   - Input: `peirce_output`.

3. **Hegel — Arbitrator** ([`subagents/hegel.md`](subagents/hegel.md))
   - Resolves every audited claim by comparing Peirce's reasoning, Socrates'
     challenge, and the source material.
   - Classifies each resolution as verified fact, verified logic, refined, or
     downgraded assumption.
   - Produces a standalone verified specification, risk register, and complete
     decision provenance so readers do not need the intermediate reports.
   - Inputs: `peirce_output`, `socrates_report`, `source_code`.

### Workflow

```text
User prompt + input data
          |
          v
Peirce: deliverable + Logic Log
          |
          v
Socrates: claim-by-claim audit
          |
          v
Hegel: arbitration against source material
          |
          v
Standalone verified document
```

### Intended Use

Use this group for specifications, technical analyses, descriptions, and other
non-code tasks where claims need explicit reasoning and independent review.
The source material passed to Hegel remains authoritative for what is currently
implemented, while Hegel may still identify bugs, uncertainty, and technical
debt in that implementation.

The workflow depends on complete handoffs: Socrates must receive Peirce's full
output, and Hegel must receive both prior outputs plus the relevant source
material. Skipping stages removes either independent challenge or final
evidence-based arbitration.

## Slop Auditor

**Plato — Clean-Slate Hindsight Agent**
([`subagents/plato.md`](subagents/plato.md)) audits AI-produced branch history
for "neuroslop": later patches that compensate for weak architectural choices
in earlier commits.

Use Plato when testing a branch with several agent-authored commits. It reveals
when follow-up fixes should be folded into earlier design decisions, then
proposes the clean architecture that hindsight makes possible.

- Inputs: `branch_diff`, `base_branch_context`.
- Output: extracted requirements, a five-heuristic slop audit, the clean-slate
  design, and a step-by-step refactoring action plan.