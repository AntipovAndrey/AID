# Incremental Comprehension Audit: README.md and skills/audit-claims/SKILL.md

Status: `PARTIAL`. README.md was read in full (22 units). The skill was sampled:
its description and first paragraph (12 units); the rest of the skill was not
audited.

## 1. Scope

- Text: `README.md` at commit `c9d88eb` plus uncommitted edits; `skills/audit-claims/SKILL.md`, uncommitted.
- Audience simulated: a developer who found the repository on GitHub, no prior
  knowledge of it. No audience instruction was given to readers.
- Readers: one fresh Claude Sonnet agent per prefix, tools forbidden, no
  repository access, no knowledge of the audit. 34 readers total.
- Questions: the six from the skill, same order every time.

## 2. Units

### README.md

```text
U1  # AID Skills and Subagents
U2  Reusable skills and subagent definitions for auditing documents, code structure, and agent-authored branches.
U3  Every file is plain Markdown:
U4  use it as a Claude Code plugin,
U5  or paste a skill or agent into any other model as a prompt.
U6  ## Install as a Claude Code plugin
U7  [code] /plugin install aid --marketplace AntipovAndrey/AID
U8  To try it without installing, run `claude --plugin-dir .` from the repository root.
U9  ## Use
U10 [code] six /aid:<skill> example commands
U11 Subagents can also be called directly: `@agent-aid:peirce`, `@agent-aid:socrates`, `@agent-aid:hegel`, `@agent-aid:plato`.
U12 The `audit-claims` and `audit-branch-slop` skills run them in the right order with full handoffs.
U13 ## Index
U14 [table header] Category | Skill / Subagent Guide | Method
U15 [row] Subagent Guides | Logic Auditors — Peirce, Socrates, Hegel | ...
U16 [row] Skills | Audit Logically (skills/audit-claims) | ...
U17 [row] Skills | Detect Branch Slop (skills/audit-branch-slop) | ...
U18 [row] Skills | Feature Research to Spec (skills/spec-feature) | ...
U19 [row] Skills | Module Decomposition Analysis (skills/decompose-module) | ...
U20 [row] Skills | Incremental Comprehension Audit (skills/audit-comprehension) | ...
U21 [row] Skills | Module Naming Consistency (skills/audit-naming) | ...
U22 [row] Subagent Guides | Incremental Changes Slop Detection — Plato | ...
```

### skills/audit-claims/SKILL.md (sampled)

```text
V1  name: audit-claims
V2  description: Produce a non-code deliverable (...) whose every significant claim is traceable, independently challenged, and resolved against source material.
V3  Runs three subagents in sequence:
V4  Peirce writes the deliverable with a Logic Log,
V5  Socrates attacks every logged claim,
V6  Hegel arbitrates and emits a standalone verified document.
V7  Use when the first reader of the document will act on its claims and the cost of a wrong claim is high.
V8  Not for writing code, not for style editing.
V9  # Audit Claims
V10 A single agent writing a document silently turns its early guesses into premises for later conclusions.
V11 This workflow forces every significant claim to be labelled by its reasoning type, attacked by a reader who did not write it, and resolved by a third reader who holds the source material.
V12 The output is one document a reader can trust without seeing the intermediate reports.
```

## 3. Reading log (condensed; full answers in the session transcript)

### README.md

```text
U1  subject: "something called AID Skills and Subagents"        confidence: low
    expects: what AID is                                        unclear: AID, skill vs subagent
U2  subject: collection for auditing docs/code/branches         [EXTENSION]  SubjectOnset
    expects: list or index of skills                            unclear: AID, platform, "auditing"
U3  subject: unchanged                                          [EXTENSION]  expectation missed
    expects: file types / layout                                unclear: what the colon introduces
U4  subject: unchanged, "Claude Code plugin" settled            [EXTENSION]  expectation missed
    expects: other ways to use
U5  subject: unchanged                                          [EXTENSION]
    expects: install instruction or list
U6  heading                                                     [EXTENSION]
    expects: install command
U7  install command settled                                     [EXTENSION]
    expects: what is in the collection
U8  plugin-dir settled                                          [EXTENSION]  expectation missed
    expects: section heading or list of skills
U9  heading                                                     [EXTENSION]
    expects: /aid:<skill> invocation
U10 six skill names settled                                     [EXTENSION]
    expects: what each skill does                               unclear: "slop", is the list complete
U11 four subagent names settled                                 [EXTENSION]
    expects: contents table                                     unclear: philosopher names, which agent backs which skill
U12 two skills orchestrate subagents                            [EXTENSION]  expectation missed
    expects: what each skill does / layout / end                unclear: "full handoffs"
U13 heading                                                     [EXTENSION]
    expects: table of skills and agents
U14 table header                                                [EXTENSION]
    expects: first row                                          unclear: what "Method" will hold
U15 Peirce/Socrates/Hegel roles settled                         [EXTENSION]
    expects: more rows, Plato                                   unclear: Plato absent, "claims", "verified documents"
U16 "Audit Logically" row                                       [EXTENSION]  confidence in settled facts drops to medium
    unclear: "Audit Logically" vs audit-claims; "index lists only one skill when Use shows six"
U17 "Detect Branch Slop" row                                    [EXTENSION]
    unclear: labels do not match skill names; "hindsight refactoring plan"; four skills still unindexed
U18 "Feature Research to Spec" row                              [EXTENSION]
    expects: end of text                                        unclear: is audit-claims the same as Audit Logically
U19 "Module Decomposition Analysis" row                         [EXTENSION]  expectation missed (expected end)
    unclear: audit-comprehension and audit-naming not indexed
U20 "Incremental Comprehension Audit" row                       [EXTENSION]  expectation missed (expected end)
    unclear: Plato missing from index; audit-naming used but not indexed
U21 "Module Naming Consistency" row                             [EXTENSION]
    unclear: whether audit-comprehension and audit-naming "exist as skills"
U22 Plato row                                                   [EXTENSION]
    unclear: table mixes categories, Plato row last; whether the four other skills use subagents
```

### skills/audit-claims/SKILL.md

```text
V1  subject: a name field                                       confidence: low
V2  subject: skill producing verified non-code documents        [EXTENSION]  SubjectOnset
    expects: trigger condition "Use when..."                    unclear: "independently challenged", "significant", source material
V3  three subagents                                             [EXTENSION]  expectation missed
    expects: the three named
V4  Peirce                                                      [EXTENSION]
V5  Socrates                                                    [EXTENSION]
V6  Hegel                                                       [EXTENSION]
    expects: when to use                                        unclear: predefined or ad hoc subagents; who supplies sources
V7  trigger                                                     [EXTENSION]
V8  exclusions                                                  [EXTENSION]
V9  heading                                                     [EXTENSION]
V10 problem statement                                           [EXTENSION]
V11 mechanism                                                   [EXTENSION]
    settled: "Hegel has the source material"                    unclear: reasoning types; fresh contexts
V12 output                                                      [EXTENSION]
    settled: "Hegel is the one who holds the source material"   unclear: "whether the source material is given to Peirce too"
```

## 4. Metrics

### README.md

| Metric | Value |
| --- | --- |
| SubjectOnset | U2 |
| Revisions | 0 |
| CollapsePoint | none |
| StallRate | 0 / 22 |
| WrongExpectations | U2→U3, U3→U4, U7→U8, U11→U12, U18→U19, U19→U20 |

Three of the six misses (U2→U3, U7→U8, U11→U12) are the same miss: the reader
expected the list of contents and received installation or packaging detail.
Two (U18→U19, U19→U20) are readers believing the table had ended.

Persistent unclarities, by first and last unit where a reader raised them:

| Unclarity | Raised at |
| --- | --- |
| What "AID" stands for | U1–U22, every reader |
| Skill vs subagent | U1–U12 |
| What "slop" means | U10–U22, every reader |
| Which subagent backs which skill | U11–U22 |
| "full handoffs" | U12–U22 |
| Index labels do not match command names | U16–U22, every reader |
| Skills seen in Use but not yet in Index | U16–U21 |
| Plato absent from Index until the last row | U15–U22 |
| "hindsight refactoring plan" | U17–U21 |

### skills/audit-claims/SKILL.md (sampled)

| Metric | Value |
| --- | --- |
| SubjectOnset | V2 |
| Revisions | 0 within the sample |
| CollapsePoint | none |
| StallRate | 0 / 12 |
| WrongExpectations | V2→V3 |

Persistent unclarities: what counts as a "significant" claim (V2–V12, every
reader); what a Logic Log contains (V4–V12); what the source material is and who
supplies it (V2–V12); whether the three subagents are predefined (V6–V12). At
V11 and V12 readers settled "Hegel holds the source material" and asked whether
Peirce receives it too. Steps 2 and 3 of the skill give the sources to all three
stages, so this belief will be overturned later in the text: a latent REVISION
outside the sample.

## 5. Findings

### README.md

F1. Index labels are the old skill names. U16–U22. Every reader from U16 on
asked whether "Audit Logically" is `audit-claims`, and readers at U16–U21
believed skills shown in Use were missing from the Index because they were
searching for the command name. Mechanism: the same thing under two nouns.
Change: make the Index label the command name, in the Use order.

F2. The contents are expected before installation. U2→U3, U7→U8, U11→U12.
Readers asked "what is in the collection" three times before the Index at
U13. Mechanism: the subject is named at U2, but what it consists of arrives
after how to install it. Change: either move Index above Install, or add one
sentence after U2 naming the six skills and four subagents. The second keeps
the install command high on the page.

F3. "AID" is never expanded. U1–U22. Mechanism: a term used before it is
introduced. Change: one clause in U1 or U2, either an expansion or "AID is a
name, not an acronym".

F4. Skill and subagent are undefined until U12, and the relation is settled
only for two skills. U1–U12, U22. Mechanism: two terms used before introduction;
a list whose items belong to different categories. Change: one sentence in the
intro: "A skill is a slash command that tells Claude what to do; a subagent is a
role a skill launches. Four skills run alone; two run the subagents."

F5. "slop", "full handoffs", "hindsight refactoring plan" are unexplained.
U10–U22. Mechanism: jargon in place of a predication. Change: "slop" → one
gloss at first use ("accidental complexity accumulated by incremental fixes");
"with full handoffs" → "passing each stage's complete output to the next";
"hindsight refactoring plan" → "the refactoring a developer would have done
knowing all requirements up front".

F6. The Index mixes categories, and Plato's row is last. U15, U22. Readers at
U15 noticed Plato's absence immediately after the Logic Auditors row. Mechanism:
a list whose items belong to different categories, out of order. Change: place
the Plato row directly after the Logic Auditors row, then the skills; or sort by
category.

F7. Readers believed the table had ended at U18 and U19. Mechanism: no signal
of table length. Low priority; F1 and F2 reduce the effect because the reader
will have already seen the full list of names.

### skills/audit-claims/SKILL.md

F8. "significant claim" is undefined in the skill and in `peirce.md`. V2–V12.
Peirce's prompt says "every significant claim, specification, or detail" without
a criterion. Change: add to Peirce's instructions one test, for example "a
claim is significant when a reader acting on the document would act differently
if it were false".

F9. "a third reader who holds the source material" implies only Hegel has the
sources. V11–V12. Steps 2–4 give the sources to all three stages. Change: "and
resolved by a third reader against the source material" or state explicitly that
every stage reads the sources and Hegel treats them as authoritative.

F10. Readers could not tell whether Peirce, Socrates, Hegel are predefined
agents or roles to improvise. V6–V12. Change: in the description, "Runs the
plugin's three subagents in sequence" or name them as `peirce`, `socrates`,
`hegel` agents.

F11. "Logic Log" and "reasoning type" are introduced without content. V4–V12.
The body defines neither; `peirce.md` does. Change: one clause in V11: "labelled
as deduction, induction, or abduction".

## 6. Edit list

README.md, in order of effect:

1. Rename Index labels to command names, reorder rows to the Use order, place
   the Plato row after the Logic Auditors row.
2. After U2, add: "Six skills are slash commands; four subagents are roles that
   two of the skills run in sequence."
3. In U1 or U2, expand or disclaim "AID".
4. Gloss "slop" at U10 or in the Index row; replace "with full handoffs" and
   "hindsight refactoring plan" with plain predications.

skills/audit-claims/SKILL.md and subagents/audit-claims/peirce.md:

5. V11: "resolved by a third reader against the source material"; add that all
   stages read the sources.
6. Description: "Runs the plugin's `peirce`, `socrates`, and `hegel` subagents
   in sequence".
7. V11: name the three reasoning types.
8. peirce.md: define "significant claim" with a test.

Retest: not run. Metrics after the edits are to be measured with readers who
have seen neither version.
