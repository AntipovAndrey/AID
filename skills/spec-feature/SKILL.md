---
name: spec-feature
description: >-
  Turn a feature request with many unknowns into an implementable technical
  specification: ground in the running system first, then grow one living goal
  document by asking the requester a single question at a time and recording only
  confirmed facts. Use when scenarios, requirements, and scope must be discovered
  before any design decision. Not for implementing an already specified feature,
  and not for producing a set of UML documents.
---

# Feature Research to Spec

The deliverable of research is an agreed mental model and a specification someone
can implement, not a documentation set. Every artifact must either hold a fact the
requester confirmed or change the next decision. An artifact that only restates
already agreed facts in another notation is waste, and keeping it alive costs a
rewrite after every new decision.

Guessing is the primary failure mode. A guess written into a document becomes a
requirement nobody agreed to, and later conclusions silently rest on it. A guess
must leave your mouth as a question, never enter a file as a statement.

## Scope

- Establish where research files live and keep everything in that one directory.
  Put stand runtime data, downloaded tools, and generated files in an ignored
  subdirectory.
- The requester owns the facts. You own the model's consistency, the codebase
  evidence, and the questions.
- Do not change production code until the specification is agreed. Do not let
  research decisions leak into unrelated modules.
- Separate three things at all times: how the system behaves today, what the
  requester decided, and what is still open. Never merge them into one voice.

## Artifact budget

Durable, worth maintaining to the end:

- **One goal document.** The task statement verbatim, the agreed mental model with
  its cardinalities, the boundaries of the first version, the open questions, and
  the working rules. Every new decision is back-propagated into it immediately, so
  it stays the single place to re-read before answering anything.
- **One specification**, written once the model stops moving.

Useful only while facts are still arriving:

- Per-stage notes for actors and use cases, then for flows. They pay off while
  each line is a freshly confirmed answer. Once their content is fully implied by
  the goal document, stop growing them and fold the substance into the spec.

Do not produce:

- **A rendered diagram gallery.** Source plus image files per flow, a local
  renderer, and a review round per picture. Each model change invalidates all of
  them, and the redraw-and-confirm loop yields agreement about the drawing, not a
  decision about the system.
- **Sequence diagrams as a final analysis stage.** By the time the flows are
  agreed, they encode nothing the specification does not state more precisely.
  Planning this stage up front creates an obligation that gets silently dropped.
- **A document written for the requester to review.** See stage 2.

At most one small inline diagram, inside the goal document, and only when it
disambiguates cardinalities or a fan-out that prose keeps getting wrong.

## 0. Ground in the running system

Do this before the first question. Questions asked without evidence are generic,
and they force the requester to teach you the system instead of deciding about it.

- Find and read the project's own architecture and setup knowledge artifacts.
  Prefer an existing local-stand instruction over inventing a setup.
- Run the relevant part of the system, or at least the scenario-level tests that
  exercise it end to end. Read the code path the feature will touch.
- Record what currently happens in the goal document as current behavior, kept
  visibly apart from anything the feature requires.

Exit: you can describe today's path from external event to effect, naming real
components, and your first questions are about choices rather than facts you could
have read.

## 1. Open the goal document

Write the task statement exactly as given, then a status line saying the model is
a draft. Add the working rules you will be held to: what gets committed and when,
that hypotheses do not become requirements, that implementation details do not
become requirements.

Leave the mental model section nearly empty. It is filled by stage 2, not by you.

## 2. Elicit facts interactively

Ask one question. Write down the answer. Ask the next one.

- Never hand over a drafted document and ask for corrections. The requester wants
  to be asked, not to proofread an invention. If you already wrote one, delete it
  and restart the section from questions.
- Ask about a concrete situation, not about a preference: two events arriving at
  once, an entity moved while it is in use, a participant removed mid-flow.
  Concrete situations surface rules that abstract questions never reach.
- Record only what was confirmed. Anything else goes to the open questions list
  with the name of the thing that is undefined.
- A working name is allowed and must be marked as one, together with what it is
  explicitly not.
- Do not re-ask what already follows from the agreed model. Ask only about
  decisions the model does not imply, and about contradictions with earlier
  answers. Point out the contradiction rather than choosing a side.
- Never promote an implementation detail to a requirement. A configuration flag or
  a class found in the code is context for a question, not a scenario. When you
  catch yourself doing it, say so and drop it.
- After each accepted answer, propagate it into the goal document and revisit the
  statements it invalidates, including ones agreed earlier. A decision that
  contradicts an earlier one must visibly retire it.
- Commit each section once the requester agrees it is settled, with a stable
  prefix, so the agreed state is recoverable and the diff shows what a session of
  questions actually produced.

Exit this stage when new answers start being consequences of the model rather than
additions to it, or when the requester asks for the specification. Both mean the
unknowns are gone; further questions only spend attention.

## 3. Write the specification

Before writing, read the project's architecture and code-style artifacts for every
component the feature touches. The specification must be expressed in the
vocabulary, layering, and naming conventions those artifacts already define.
Inventing a parallel style guarantees rework.

The specification states, per component:

- The goal in one paragraph, and the domain model with its operations and
  atomicity rules.
- The key shape that must exist at some layer, named explicitly. Fixing the shape
  and its name is the point; expect the requester to rename it, and adopt the new
  name everywhere at once.
- The work required in each component, phrased as contracts and behavior rather
  than as file lists.
- Architectural constraints inherited from the project, the boundaries of the
  first version, including what is deliberately a stub, and the done criteria.

Be dense. This document is read to be implemented, not to be admired.

## 4. Implement in reviewable steps

- Prefer extending and renaming an existing module over creating one, when the new
  data belongs with data that module already owns.
- Commit step by step, so a wrong turn costs one step.
- Mark deliberate shortcuts in place with an explicit annotation or a one-line
  marker, so they are greppable instead of archaeological.
- Keep the stubs the specification declared as stubs. Silently implementing more
  than was agreed reintroduces guessing at the last possible moment.

## Verification

Before calling research done:

1. Every statement in the goal document traces to a confirmed answer or to code
   you read, and current behavior is still distinguishable from new requirements.
2. The open questions list is either empty or explicitly accepted as out of scope
   for the first version.
3. The specification is derivable from the goal document alone, and the working
   names in it are the ones the requester approved.
4. No artifact exists that you would have to rewrite after the next decision but
   that nobody reads to make one.
