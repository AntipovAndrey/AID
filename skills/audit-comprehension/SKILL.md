---
name: audit-comprehension
description: >-
  Test how a text is understood while it is being read: feed growing prefixes of
  one clause at a time to a fresh reader for each prefix, record what that reader
  infers, and find the unit where the forming understanding has to be revised or
  collapses. Use for documents, READMEs, specifications, and prompts whose first
  reading matters, not for style editing or fact checking.
---

# Incremental Comprehension Audit

A reader is not a model with the whole file in context. People commit to an
interpretation after the first words and then refine it. Refinement is cheap,
revision is expensive, and a text that forces revision after every sentence is
abandoned. Measure where the forming understanding breaks, then fix the text so
every later unit only narrows the interpretation the earlier ones created.

This audit does not judge whether the text is correct, complete, or well written.
It judges only what a first-time reader believes after each unit.

## Scope

- Establish the source text, its revision, the audience to simulate, and where the
  report goes. Audit one document per run; a quoted excerpt is a separate text.
- The text is read-only during measurement. Fix it after the readings, never
  between prefixes of the same run.
- Readers must be fresh and uninformed: no earlier prefix, no later text, no
  repository access, no knowledge of this audit's purpose. One reader per prefix.
- A reader that has seen unit `k+1` can never again report on prefix `k`. Discard
  it rather than reuse it.

## 1. Split the text into units

Number the units in source order and never reorder, merge, or reword them. A unit
is one predication: one verbal group with its subject and objects. Split compound
and complex sentences so that each clause that asserts something is its own unit,
including subordinate clauses that carry a condition or a cause.

Treat each heading, list item, table row, code block, and diagram as its own unit
at the position where it appears. A multi-line artifact is one unit; do not split
it into lines. Record the unit list verbatim, with IDs, in the report.

Keep the original formatting inside a unit. If the text is translated or
normalized before the audit, say so, and audit only one version.

## 2. Run the prefix readings

For every unit `k`, give a new reader exactly units `1..k` and nothing else: no
title unless the title is unit 1, no filename, no surrounding context, no
instruction about the domain. Ask the same questions in the same order every time:

1. What is this text about? Name the subject in one sentence.
2. Who is it for, and what does it ask that reader to do or believe?
3. What do you already consider settled?
4. What do you expect the next sentence to say?
5. What is unclear or ambiguous right now?
6. How confident are you in answers 1–3: high, medium, or low?

Record the answers verbatim. Do not paraphrase, correct, or argue with a reader.
Do not reveal that the text continues beyond the prefix. If a reader asks for
clarification, note the question as evidence and end that reading.

```text
U1  "We keep AGENTS.md in the repository."
    subject: a convention about a file kept in a repo
    settled: the file exists; the repo is the team's
    expects next: what AGENTS.md contains
    unclear: what an agent is here; who "we" are
    confidence: medium

U2  "Each agent reads it before answering."
    subject: unchanged, narrowed to agent behavior   [EXTENSION]
    ...
```

For long texts, read every unit of the first two paragraphs and every unit that
introduces a term, a subject, or a conclusion; sample the rest at a fixed stride
and state the stride. Never skip a unit you expect to be a problem.

## 3. Classify every transition

Compare reading `k-1` with reading `k` and label the transition:

- `EXTENSION`: the subject holds and new facts are added.
- `REFINEMENT`: an earlier belief becomes more specific without being contradicted.
- `REVISION`: an earlier belief is now wrong. Record the belief, the unit that
  created it, and the unit that overturned it.
- `COLLAPSE`: the reader cannot state the subject, states a different subject, or
  reports contradictory settled facts.
- `STALL`: nothing changed; the unit added no information for this reader.

Report, with unit IDs rather than scores alone:

- `SubjectOnset`: the first unit after which the subject is stated correctly and
  stays correct. A late onset means the text explains before it says what it is.
- `Revisions`: count and positions. Weight early revisions more heavily; a reader
  who must rebuild an interpretation at unit 3 rarely reaches unit 30.
- `CollapsePoint`: the first `COLLAPSE`, if any. Everything after it is unverified.
- `StallRate`: `STALL` units over audited units.
- `WrongExpectations`: units where the stated expectation for the next sentence
  contradicts what the next unit actually does.

Expectation misses are the signal of structure, not of reader quality. Do not
average the metrics into one number and do not blame a reader for a miss.

## 4. Diagnose the cause at each failure

For every `REVISION`, `COLLAPSE`, and early `WrongExpectation`, name the mechanism
in the text, quote the units, and propose a concrete change. Usual causes:

- The subject arrives after the explanation, or the first unit starts mid-argument.
- A term, product, or role is used before it is introduced, or the same noun
  silently changes meaning.
- A pronoun or `this` whose antecedent is a whole previous paragraph.
- Two or more claims packed into one sentence, so the reader settles the wrong one.
- A conclusion stated before the fact it depends on, forcing a rewind.
- Horizontal ASCII art, wide tables, or inline enumerations used as structure. A
  vertical structure that mirrors the real file layout is read; a horizontal
  diagram is skipped.
- A list whose items belong to different categories, so item 3 invalidates the
  rule the reader derived from items 1 and 2.

Separate defects of order from defects of content. Missing information is a gap
for the author; information in the wrong place is this audit's finding.

## 5. Fix the order, then retest

Rewrite so that unit 1 names the subject, each following unit only extends or
refines it, and anything a later unit depends on appears earlier. Prefer moving,
splitting, and naming over adding words. Do not delete content to raise a metric.

Retest from unit 1 with readers who have never seen either version, and report
before and after side by side for the same metrics. A fix is accepted when
`SubjectOnset` is unit 1 or 2, there is no `COLLAPSE`, and no `REVISION` remains
in the first third of the text. Revisions deliberately kept for effect must be
listed with their reason.

## Report

One Markdown document, in this order:

1. Scope: text, revision, audience simulated, sampling stride, reader setup.
2. The numbered unit list verbatim.
3. The reading log: per prefix, the six answers and the transition label.
4. Metrics with unit IDs, and the collapse point if there is one.
5. Findings: each failure with its mechanism, quoted units, and proposed change.
6. The rewritten text or an ordered edit list, plus the retest metrics.

Mark a run `PARTIAL` when units were sampled, a reader was contaminated, or the
text changed mid-run. Do not present an unverified tail as audited.
