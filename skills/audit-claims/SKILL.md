---
name: audit-claims
description: >-
  Produce a non-code deliverable (specification, technical analysis, description,
  decision record) whose every significant claim is traceable, independently
  challenged, and resolved against source material. Runs the plugin's `peirce`,
  `socrates`, and `hegel` subagents in sequence: Peirce writes the deliverable with
  a Logic Log, Socrates attacks every logged claim, Hegel arbitrates against the
  sources and emits a standalone verified document. Use when
  the first reader of the document will act on its claims and the cost of a wrong
  claim is high. Not for writing code, not for style editing.
---

# Audit Claims

A single agent writing a document silently turns its early guesses into premises
for later conclusions. This workflow forces every significant claim to be labelled
as deduction, induction, or abduction, attacked by a reader who did not write it,
and resolved by a third reader against the source material. All three stages read
the sources; the third treats them as authoritative. The output is one document a
reader can trust without seeing the intermediate reports.

## Scope

- One deliverable per run. The user's task, the input material, and the report
  directory are fixed before the first subagent starts.
- Every stage's output is written to disk as it arrives. The intermediate
  reports are the audit trail: a reader who doubts a resolution in the final
  document must be able to open the challenge and the original claim.
- Stages run strictly in order. Each stage receives the complete output of the
  previous one, pasted verbatim into its prompt. Summaries break the chain:
  Socrates cannot attack a claim it was not shown, and Hegel cannot resolve a
  challenge it did not read.
- Subagents are fresh. Do not reuse the Peirce agent as Socrates or Hegel.

## 1. Gather inputs

Establish:

- `user_prompt`: the exact task, in the user's words.
- `input_data`: the material the deliverable is based on. Prefer paths the
  subagent can read over pasted excerpts; paste only when the material is not
  on disk.
- The source of truth Hegel will arbitrate against. For technical documents
  this is the code or the configuration; for other documents, the primary
  sources. Name the paths.
- The report directory. Default to `docs/audits/<yyyy-mm-dd>-<slug>/` in the
  repository, or ask if the repository has an established place for reports.

If the task is ambiguous, ask the user now. Do not let Peirce resolve ambiguity
by assumption.

## 2. Run Peirce (Analyst)

Launch the `peirce` agent. The prompt contains, under these headings:

- `## User prompt`: `user_prompt` verbatim.
- `## Input data`: `input_data` or the paths to read.

Peirce returns the deliverable interleaved with Logic Log tables. Do not edit it.
Write it to `<report-dir>/01-peirce.md` and keep it as `peirce_output`.

## 3. Run Socrates (Auditor)

Launch the `socrates` agent. The prompt contains:

- `## Peirce output`: `peirce_output` in full.
- `## Source material`: the same paths given to Peirce, so factual checks can
  read the sources.

Socrates returns one challenge per Logic Log entry plus a summary. Confirm the
count of challenges equals the count of Logic Log entries. If any entry was
skipped, send the agent the list of missing IDs and ask it to complete the
audit before continuing. Write the complete text to `<report-dir>/02-socrates.md`
and keep it as `socrates_report`.

## 4. Run Hegel (Arbitrator)

Launch the `hegel` agent. The prompt contains:

- `## Peirce output`: `peirce_output` in full.
- `## Socrates report`: `socrates_report` in full.
- `## Source material`: the paths Hegel must treat as authoritative.

Hegel returns a standalone document: original objective, verified specification,
risk register, decision provenance. Confirm the provenance section resolves every
Logic Log ID. If any is missing, ask Hegel to complete it. Write the document to
`<report-dir>/03-hegel.md`.

## 5. Deliver

Give the user Hegel's document unchanged. Report, in one short block:

- Counts: Logic Log entries, challenges, resolutions by category.
- The High-risk items from the risk register, each in one line.
- The three report paths.

Do not merge your own opinion into the verified specification. If you disagree
with a resolution, say so after the document, with the Logic Log ID.
