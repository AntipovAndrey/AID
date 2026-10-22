---
name: audit-naming
description: >-
  Audit naming and contract consistency within a module by statically tracing API
  and framework entry points through declared interfaces and actual implementations
  to the module boundary. Produce one Markdown document containing the complete
  call trace, evidence-based naming problems, and an actionable refactoring brief
  with measurable acceptance criteria. Use for naming, responsibility, type ownership
  and nesting, and semantic clarity reviews, not for implementing the refactoring
  or running the application.
---

# Module Naming Consistency

Determine whether a reader can predict the subject, operation, ownership, and
important effects from a contract, then verify those predictions against actual
call paths. Consistently vague names are not good names. Renaming every component
to `Service`, `Manager`, or `Handler` is not a solution.

## Scope and permissions

- Work with any module, language, and architecture. Follow its local guidelines;
  do not impose this repository's stack, package layout, or domain vocabulary.
- Audit statically. Do not start the application, run tests/builds, invoke business
  operations, or contact live dependencies. Read tests as evidence of intended
  behavior, not proof that they pass. Read-only source and Git inspection is allowed.
- Write exactly one Markdown report. Do not modify production code, tests,
  configuration, or existing knowledge artifacts during this audit. List required
  documentation/skill updates in the refactoring brief instead.
- Do not stage, commit, stash, reset, switch revisions, or overwrite others' work.
  Do not create extra snapshots or report files without permission. Never copy
  secrets into the report. If file writing is unavailable, return the same complete
  Markdown document and state that it was not saved.
- Use available source navigation, file search, and reading tools. Do not require
  an IDE index, a specific MCP server, shell command, subagent, or renderer. A plain
  file-reading agent must be able to perform this workflow.

## 1. Establish the boundary and source version

Identify the module's exact source roots/packages, public API, framework entry
points, relevant tests, exclusions, and report path. A module may be a package,
not a build project. Ask only when the boundary or source version is ambiguous.
Unless specified otherwise, audit the current working tree and use
`<module-docs>/<module-name>-naming-review.md`; avoid overwriting an unrelated report.
Use the user's language for the report unless requested otherwise.

Record the revision, timestamp with time zone, dirty/untracked files in scope, and
a manifest of inspected files with content hashes. When reviewing a commit, pin
its immutable ID and never mix it with working-tree contents. During concurrent
edits/rebases, recheck hashes and the scoped file inventory before finalizing.
Refresh affected traces consistently or mark the report `PARTIAL`; a stable
`HEAD` alone does not prove that the working tree stayed unchanged.

Stop recursive tracing at the first call outside the module, including libraries.
Inspect outside declarations, callers, or DI registration only as needed to
identify entry points, bindings, and compatibility obligations; do not expand their
bodies. An in-module callback invoked by an external framework is a separate
in-module root, not permission to traverse the framework. Respect explicit user
exclusions rather than silently shrinking the module for convenience.

## 2. Inventory independently of the call tree

Build numbered inventories directly from the scoped sources before tracing:

- `R`: every API operation and implicit production entry point: HTTP/RPC handlers,
  commands, listeners, scheduled jobs, filters, lifecycle hooks, registered callbacks,
  and DI factories with handwritten behavior. Include uncalled public API methods.
- `M`: every handwritten production callable declaration and implementation,
  including private helpers, overloads, top-level/extension functions, meaningful
  constructors/accessors, default interface bodies, mappers, and callback bodies.
  Give interface declarations and implementing bodies separate IDs and link them.
- `E`: every relevant call site in those bodies: internal calls and external
  boundary calls. Identify an edge by caller plus source location, not callee name.
  Record repeated calls separately; a loop is one site with its multiplicity noted.
- `C`: every contract operation for the clarity review: public APIs, internal
  application contracts, dependency ports, and equivalent concrete entry points
  where no interface exists. Do not exclude an operation because it lacks an interface.
- `T`: every handwritten data/value type passed between components, plus private
  result/projection types. Include both standalone and already nested types; identify
  producer methods, consumer signatures, and the semantic owner independently of
  current file placement. Inspect boundary consumers' declarations without tracing
  their bodies outside the module.

Exclude compiler-generated boilerplate, trivial field access, and routine pure
standard-library operations explicitly and consistently. Never exclude meaningful
I/O, locks, transactions, state changes, callback dispatch, or domain logic because
they occur in a helper, initializer, getter, or lambda. Inventory important domain
types, IDs, parameters, result variants, status fields, and DI dependency names too.

Resolve bindings from constructors, factories, registration, qualifiers, profiles,
and decorators. Record evidence for each interface-to-implementation mapping.
Include every applicable production alternative with its activation condition;
do not substitute a test fake or guess `FooImpl` from `Foo`.

## 3. Trace every root to the boundary

Use this literal form for each call, retaining both the receiver's declared type
and the actual implementation. Include signatures where needed to distinguish
overloads and source references for declarations, call sites, and implementations.

```text
R1 ModuleEndpoint.submit(Request) [direct; HTTP; module/ModuleEndpoint.kt:20]
  calls SubmissionRequests.requestSubmission(Request) [E1; declared interface; module/ModuleEndpoint.kt:22]
    implemented as SubmissionRequestsImpl.requestSubmission(Request) [module/SubmissionRequestsImpl.kt:18]
    [invalid request] returns Rejected; no write
    [valid request] calls SubmissionScheduleStore.scheduleSubmission(Request) [E2; declared interface; module/SubmissionRequestsImpl.kt:24]
      implemented as SqlSubmissionScheduleStore.scheduleSubmission(Request) [module/SqlSubmissionScheduleStore.kt:30]
      calls SqlSubmissionScheduleStore.toRow(Request) [E3; direct/private; module/SqlSubmissionScheduleStore.kt:32]
        [leaf; pure mapping; module/SqlSubmissionScheduleStore.kt:45]
      calls Database.execute(Statement) [E4; external library; module/SqlSubmissionScheduleStore.kt:34; STOP]
        [effect declared in module: persist pending submission]
    returns Scheduled; submission has NOT executed

R2 SubmissionWorker.processDueSubmissions() [direct; scheduler; separate root; module/SubmissionWorker.kt:15]
  [expand all calls using the same format]
```

The example is notation, not an architecture to copy. The actual report must not
contain placeholder subtrees or ellipses instead of inspected calls.

- Label concrete receivers `[direct]`; show any interface they implement separately
  without pretending the call used that interface. Preserve overload distinctions,
  decorators, delegation, and actual dispatch. Do not invent interfaces for free functions.
- Mark generated repositories/proxies as `[generated implementation]`, cite their
  declaration and in-module query/mapping, then stop at framework internals. For
  unresolved dispatch, write `[UNRESOLVED: reason and candidates]`; never invent a body.
- Expand internal helpers, mapping, meaningful validation, error/catch/finally paths,
  early returns, loops, conditional calls, and state transitions. Record swallowed
  failures and the precise meaning of produced results, not just the success path.
- Distinguish invoking a callback now from registering one, enqueueing from
  executing, selecting work from claiming it, and requesting a retry from retrying.
  Cross-reference deferred execution roots; do not draw them as synchronous calls.
- Show external effects at `STOP` using evidence in the module: local read/write,
  remote call, lock, transaction, deletion, publication, or scheduling. Do not infer
  an external dependency's hidden implementation or guaranteed delivery.
- Expand shared subtrees once under stable IDs and reference them at every caller.
  Mark recursion/cycles with back-references. Reuse must not conceal branch-specific effects.
- Classify methods not reachable from known roots as test-only callers, no known
  callers, conditional roots, or unresolved reachability. Trace their outgoing calls
  in a separate subsection. A search with no matches does not prove safe deletion.
- Keep a table per operation: `symbol | promised subject/action | observed action |
  effects | immediate/deferred | result meaning | owner | evidence | mismatch`.
  Include transitive effects; a harmless-looking wrapper does not erase a write.

## 4. Critique meaning, not spelling preferences

Read the complete `DeclaredType.method(parameters): Result`, not isolated tokens.
Require a bounded capability and a recognizable subject/action without reading its
implementation. A protocol-only port such as `VendorOAuth` is unfinished: it could
exchange a code, refresh credentials, or verify identity. Depending on the actual
responsibility, `AuthorizationCodeExchange.exchangeCode` or
`AccessTokenVerification.verifyToken` expresses a capability. A low-level wire
client may legitimately use a protocol name if its methods delimit the operations.
Do not mechanically split every multi-method interface.

Look specifically for:

- One noun for different entities, IDs, representations, or scopes; multiple nouns
  for the same concept without a reason; misleading singular/plural/cardinality.
- The same verb at successive layers meaning different things, or interface,
  implementation, parameter, and file names telling incompatible stories.
- Queries that mutate/lock/call a remote service unexpectedly; `refresh` that only
  reencrypts or deletes; `invalidate` that tombstones; `notify` that merely schedules.
- Result names claiming more than their producer knows: temporary transport failure
  labeled invalid configuration, local status labeled remote availability, an
  uncertain one-shot exchange labeled safely retryable, selection reason conflated
  with credential origin. Follow every result producer and in-module consumer.
- Suffixes such as `Service`, `Registry`, `Coordinator`, `Api`, or `Port` replacing
  the missing capability rather than clarifying it; unbounded `process`/`handle`.
- Multiple owners making the same policy decision, storage secretly orchestrating
  another domain, names masking layer violations under the project's actual rules,
  or extra abstractions that only forward calls without a distinct responsibility.

A broad, explicit use case can orchestrate several actions; its name need not list
the algorithm. `Impl`, multiple interfaces per implementation, multiple layers,
short private names, and technical result variants are not automatically defects.
Separate real contract ambiguity from taste and intentional compatibility names.

For every finding, assign a stable ID and provide priority, confidence, exact
symbol/source references, a trace path, the reader's plausible wrong prediction,
the observed contradiction, its practical consequence, and a concrete correction.
Distinguish confirmed defects, supported naming risks, hypotheses, and optional
style preferences. Do not inflate counts with duplicates or invent runtime failures
from static evidence. Track unrelated behavioral bugs separately within the report.

### Producer-owned types and nesting

A type's placement should reveal whose contract it represents, including when
consumers pass it further down the call chain. Audit ownership, not just spelling.

- If a type is a producer-specific result with one semantic producer and no
  independent domain meaning, default to nesting it under that owner. Keeping it
  standalone requires a concrete, source-backed justification.
- Count semantic producers, not constructor call sites. Helpers, mappers, tests,
  and multiple implementations of one contract do not create additional owners.
  Conversely, a shared domain entity does not belong to a factory just because
  only that factory currently constructs it.
- Many consumers or forwarding components do not imply many owners. Distinguish
  passing a value along from producing a separately meaningful representation.
- Prefer the producing interface or public abstraction when one exists, not its
  implementation. Use `InstallationLookup.Summary`, not
  `InstallationLookupImpl.Summary`, for a lookup contract's projection. A private
  implementation detail may belong to its concrete producer.
- Preserve the owner qualification in consumer signatures and cross-component
  type references: `InstallationLookup.Summary`, not an imported bare `Summary`
  or an alias hiding the owner. Nesting without visible ownership misses the goal.
- Do not expose persistence/transport implementation types through higher-level
  public contracts to satisfy nesting. Choose the appropriate contract owner or
  map the producer-specific representation at the boundary. Truly independent
  domain models remain standalone; do not manufacture an owner for every type.
- Record actual language, framework, serialization, or dependency constraints as
  explicit exceptions with evidence. Follow local rules; for languages without
  nested types, use their supported owner-qualified equivalent. In Kotlin use a
  normal nested type, not `inner`; lexical ownership does not prove runtime provenance.

For every type in `T`, include a row in the same report:

`type ID | type | producer methods | consumer signatures | semantic owner |
current placement/qualification | proposed nested/standalone placement |
justification or exception | source evidence`

Classify ownership as confirmed or unresolved; do not count an unresolved row as
covered. Check existing nested types for the wrong owner as well as standalone
types missing an owner. Neither "used by many consumers" nor "might be reused
later" justifies a standalone producer-specific result.

## 5. Prepare an executable refactoring brief, not the refactoring

Derive one recommended target design from the findings. Do not hand the next agent
a thesaurus or a blanket instruction to "improve naming". Include:

1. An unambiguous glossary: concept, canonical term, identity, representation, and
   specifically excluded meanings. Separate wire compatibility names from internal names.
2. A mapping for every old contract/entry point: `old -> new / kept / removed`, exact
   target signatures, implementation/owner, affected callers and DI, and justification.
   Expose splits/merges. Require evidence before deleting apparently unused methods.
   For type-placement findings, also map old type -> target owner-qualified type,
   including kept/removed types, consumer signatures/imports, and justified exceptions.
3. Target call trees in the same interface/implementation notation, clearly labeled
   `PROPOSED`, with enough detail to explain each corrected promise and responsibility.
4. Ordered, bounded tasks tied to finding IDs and affected files. Separate mechanical
   renames/moves from behavior changes; specify prerequisites and the expected result
   of each task. Do not authorize unrelated fixes just because the trace found them.
5. Compatibility constraints discovered in the module: public/wire contracts,
   persistence/configuration names, identities, authorization, ordering, concurrency,
   retry/idempotency, error semantics, and secret handling, as applicable. Flag changes
   requiring approval or a migration rather than silently promising compatibility.
   For nesting/moves, check source and binary compatibility, serialization names,
   and query constructor references where applicable; lexical moves are not
   automatically behavior-neutral.
6. A focused verification matrix: affected scenario/invariant, existing or needed
   test, negative/concurrent cases where relevant, expected result, and acceptance ID.
   Identify commands from the actual project, or mark unknowns. Require the future
   implementer to run relevant tests, reproduce changed bugs before fixing them when
   possible, rerun afterwards, and never weaken tests to make the refactor pass.
7. The documentation, examples, skills, and architectural checks that will need
   updating after implementation. Preserve historical baseline evidence.

Prefer composition and minimal responsibility changes. Do not create an interface
per helper, a service per verb, compatibility facades without a consumer, or a new
framework to make the diagram look tidy. Every added boundary needs a distinct
policy, mapping, lifecycle, or I/O responsibility. Do not prescribe caching, queues,
outboxes, schema changes, or a new delivery model merely to fix naming.

## 6. Define measurable acceptance and evidence

Include baseline counts and a future acceptance ledger in the same report. Rebuild
the inventories and full trace from the final source after implementation; do not
reuse proposed diagrams as proof. Record changed denominators after splits/merges
and map all old contracts so removal cannot hide an unresolved problem.

| Metric | Calculation / rule | Required after refactoring |
|---|---|---|
| RootCoverage | Fully traced roots / all `R` | 100% |
| MethodCoverage | Traced or explicitly classified methods / all `M`; declarations link to bindings, non-root bodies still have expanded calls | 100% |
| EdgeCoverage | Resolved call sites / all `E`; internal dispatch has evidence, direct calls are explicit, external/generated calls have justified stops | 100% |
| ContractMappingCoverage | Old contracts and entry points mapped to new, kept, or justified removal / all old contracts and entry points | 100% |
| SemanticSurprises | Operation rows where the promised subject/action/effects/timing/result contradict actual behavior | 0 |
| UnfinishedCapabilities | Contracts whose type plus signatures still fail to identify a bounded capability; protocol-only ports and generic suffixes do not qualify | 0 |
| VocabularyConflicts | Unresolved conflicting meanings/synonyms within the agreed glossary | 0 |
| TypeOwnershipCoverage | Types with evidenced producers, consumers, semantic owner or independent domain role, and justified placement / all `T` | 100% |
| UnjustifiedStandaloneTypes | Single-owner producer-specific results left standalone without a concrete justification | 0 |
| TypeOwnershipBoundaryViolations | Type placements that make consumers depend on an implementation instead of its contract or violate agreed layer boundaries | 0 |
| HiddenTypeOwnerUsages | Consumer signatures or cross-component type references that hide a nested type's owner through bare imports or aliases, without an evidenced exception | 0 |
| ContractClarity | Correct independent predictions / (4 x all operations in `C`) | At least 95%; critical operations 100% |

Count each item once and list its ID, not just a score. Derive denominators from
sources independently of the trace. An unresolved edge is not resolved coverage;
uncertain reachability stays explicit even if its method body has been inspected.
Report an empty denominator as `N/A` with evidence, never silently as 100%.

After refactoring, rebuild `T` and its ownership table from actual declarations,
producer methods, and consumer signatures/imports. Reconcile each old type with
its new location, retained standalone role, or justified removal; record violations
before/after and revalidate every exception. Do not use the proposed placement table
or the percentage of nested classes as evidence that ownership became clear.

For `ContractClarity`, use a reviewer who has not participated in the audit/design
or implementation. Before revealing bodies, trace, comments, glossary, or target
rationale, give them only declarations, names, signatures, and owned result types.
Capture their answers unchanged to four questions for every operation:

1. What exact domain subject, identity, or representation is handled?
2. What action and role does this boundary offer: use case, local storage, external
   operation, computation, or another identifiable responsibility?
3. What significant effects does it promise, such as reading, writing, exchanging,
   claiming, deleting, or scheduling?
4. What is complete on return versus merely requested, and what do the results assert?

Freeze the critical-operation list and expected answers from implementation evidence
before collecting predictions. Include identity-sensitive, destructive, security,
one-shot, locking, deferred/retry, and result-certainty operations where present.
Then reveal the code/trace and compare. "Cannot determine" is a clarity failure;
do not demand guesses about internal algorithms or exact query counts. Count
ambiguous answers as misses, preserve all misses, and never reduce the denominator
or relabel critical operations after seeing scores. If no independent reviewer is
available, mark the metric `NOT VERIFIED`, not self-certified success.

Non-compensable gates: no unresolved misleading contracts or duplicate policy
owners for addressed findings; no unjustified new pass-through components; no
stale renamed-symbol references in production/tests/DI; no violations of the
project's agreed dependency rules; compatibility preserved or explicitly approved;
all applicable verification-matrix tests actually run and pass. List intentional
wire/config/schema/historical references as exceptions rather than renaming them.
Class counts, line counts, name lengths, and percentage renamed are not success metrics.

Use `metric | baseline numerator/denominator or count | after | target | evidence`.
During the audit, leave future results `NOT RUN`/`NOT VERIFIED`; invent no baseline
numbers, blind-review answers, or test outcomes. A current baseline with defects is
not an incomplete audit. Distinguish `Audit: COMPLETE/PARTIAL` from
`Refactoring acceptance: PASS/FAIL/NOT VERIFIED/BLOCKED`. Award `PASS` only when
every applicable gate is evidenced, not when an average score looks good.

## 7. Deliver one handoff document

Use this order, keeping everything in the one report rather than separate trace,
critique, plan, and acceptance files:

1. Scope, version/hash manifest, exclusions, audit status, and concise verdict.
2. Numbered inventories, binding evidence, and coverage ledger.
3. Complete as-is call trees, shared subtrees, non-root methods, effect table, and
   type ownership/placement table with producer and consumer evidence.
4. Prioritized findings with IDs and evidence; uncertainties and unrelated bugs apart.
5. Refactoring brief: glossary, mappings, owners, proposed trees, ordered tasks,
   compatibility limits, knowledge updates, and verification matrix.
6. Acceptance protocol, measured baseline, unfilled after ledger, and blockers.
7. A reserved post-refactoring section for the future agent's final source manifest,
   rebuilt trace and type ownership table, mapping closure, independent predictions,
   actual commands/results, metric deltas, remaining exceptions, and final verdict.
   Preserve the baseline.

Before delivering, check source references, inventory/trace reconciliation, every
subtree link, explicit stop boundaries, interface/implementation visibility, all
finding-to-task-to-check links, and the final file manifest. State that the audit
did not run the application or tests and did not implement the proposed changes.
The final chat response should link the report and summarize major findings and
verification gaps, not replace the full report with a short opinion.

## Invocation and portability

Example request: "Use audit-naming to review `<module-path>`. Trace
every API and framework entry point to the module boundary. Save the trace,
critique, and refactoring assignment with acceptance criteria in `<report-path>`.
Do not run code or implement the refactoring."

Keep this self-contained file at
`.junie/skills/audit-naming/SKILL.md` in the project available to the
agent. The workflow does not depend on CLI-, gateway-, or IDE-specific tools. If
automatic discovery is unavailable, explicitly ask the agent to read this file and
follow it. A remote checkout needs its own accessible copy; a local, untracked
skill is not distributed by Git. Do not stage or commit it as part of using it.