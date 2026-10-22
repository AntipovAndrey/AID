---
name: decompose-module
description: >-
  Analyze how to split a module: trace calls from entry points, group use cases,
  compare partitions by coupling and cohesion, and document the proposed modules.
  Use for module decomposition and boundary analysis, not implementation.
---

# Module Decomposition Analysis

Find module boundaries by grouping entry points and the components they use. Produce a document naming the proposed modules and showing the analysis behind the choice. Do not start with a desired module count or copy existing package boundaries.

## Scope

- Establish the source paths and document location. Clarify whether “module” means a logical boundary, build project, or deployment unit if this affects the task.
- Read the architecture rules, dependency declarations, and architecture tests. Use a reference module for layout and API conventions, not for choosing boundaries.
- Record the revision, relevant uncommitted changes, and excluded sources.
- Change only the analysis document and its supporting artifacts and links. Moving code and making commits require a separate request.

## 1. Trace entry points

List the classes, interfaces, and top-level declarations in scope. Use declarations as graph nodes; if using files instead, state how multi-class files are handled. Keep tests and generated code separate from production dependencies.

Find entry points: calls from other modules, endpoints, socket callbacks, webhooks, queue consumers, event listeners, scheduled jobs, lifecycle hooks, and SPI implementations. Bean construction is wiring, not a use case.

For each entry point, record:
- ID, class and method, trigger or HTTP method/path;
- authentication and activation conditions;
- use case and call trace to a side effect or external boundary.

Trace branches and resolve ports to implementations, including DI collections, qualifiers, profiles, and feature flags. Show asynchronous transitions as publisher → event → consumer. Check same-package references, extension functions, and ambiguous imports rather than relying on import counts.

Keep edge types distinct: calls/state access, type references, DI/lifecycle wiring, events/callbacks, and shared data access. Record a source file and symbol for each edge; mark unresolved links. An import graph is not a call graph. Include neighboring modules and external API consumers, but exclude them from internal graph metrics.

Save the node and edge lists in the document or linked artifacts.

## 2. Group roots and components

1. For each root, compute its reachable nodes `R(r)`. State which edge types the traversal follows and whether it includes the root. Do not treat type references as executed calls.
2. Group roots by use case. Several endpoints may serve one use case; a dispatcher may serve several. Keep individual entry points in the inventory, including legacy endpoints.
3. Compare reachability overlap: `J(a,b) = |R(a) ∩ R(b)| / |R(a) ∪ R(b)|`, or `N/A` for an empty union. Show the overlap matrix. Repeat without shared utilities such as logging and generic buses, listing exclusions. A shared registry alone does not justify merging use cases; unexpectedly low overlap may indicate a missed DI or event link.
4. Form components whose classes share responsibility, state, or invariants. List their classes and roots. Strongly connected components (SCCs) identify cycles, not necessarily module boundaries; an acyclic call chain can also be cohesive.
5. Account for nodes unreachable from any root: models, wiring, missed callers, or possibly dead code.

Build candidate modules by partitioning root groups and assigning components to them. Assign every internal node exactly once. For shared components, compare placing them with a consumer, merging consumers, or giving the component its own module and API. Do not extract `common` or technical layers merely because several classes use them.

Compare the current grouping, one-module baseline, use-case grouping, and nearby alternatives: merge related groups, split a disputed group, or move a shared component.

## 3. Compare partitions

Use the same nodes and edge types for every candidate. Default to unique directed pairs with unit weight, excluding self-loops. If weighting edges, explain the weights and also report unweighted results.

For each partition, calculate:
- `W`: total internal-scope edge weight; `I`: weight within proposed modules; `X`: weight crossing module boundaries. Check `I + X = W`.
- `retention = I / W` and `cut = X / W`; report `N/A` if `W = 0`. These measure edge placement, not semantic cohesion. They sum to one, so they are not independent objectives.
- Per module: node count, internal edges, incoming/outgoing boundary edges, and public contracts.
- Module dependency cycles: list nontrivial SCCs and the edges forming them, including cycles longer than two modules.

Retention alone favors one module. Also calculate modularity on an undirected projection: sum opposing edge weights and use `Q = Σ_c [l_c / m - (d_c / (2m))²]`, where `m` is total edge weight, `l_c` is weight inside module `c`, and `d_c` is its weighted degree sum. Report `N/A` if `m = 0`. Treat `Q` as supporting evidence, not the decision rule.

Reject architecture violations, then compare metrics alongside use-case independence, data ownership, and API size. Check whether excluding shared utilities or unresolved edges changes the preferred partition. If using automatic clustering, save the algorithm, version, parameters, and seed.

Run the calculations and retain inputs, commands, and results using project-approved tools. If measurements are unavailable, say so and give a qualitative comparison instead of estimated scores.

## 4. Recommend modules and migration steps

For each chosen module, give its responsibility, roots, classes, owned data and invariants, public API/SPI, internal implementation, and dependencies. Name it by responsibility rather than by its historical client. Explain why this partition was preferred over the alternatives tested.

List boundary crossings as caller → contract → implementation. Where a dependency needs changing, specify who owns the replacement contract and how the edge changes direction. Check shared tables/keys, serialization, transactions, event types, and `sealed` restrictions.

Keep measurements of the current code under the proposed grouping separate from the planned graph after refactoring.

Give the migration order and required checks: prepare contracts, move modules, remove the old module, update architecture tests and documentation. Identify compatibility constraints: HTTP paths, wire formats, configuration/storage keys, access rights, and lifecycle behavior.

## Document

Put the recommendation first, followed by the analysis from sections 1–4. Include the scope, candidate comparison table, rejected alternatives, and unresolved questions that could change the decision. Keep full inventories and calculation inputs in appendices or linked files rather than repeating them in prose.

Use small Mermaid flowcharts for branching call traces and the proposed module dependencies. Label asynchronous edges; distinguish the current graph from planned changes.

Before submitting, check node/root coverage, edge totals, cycles, diagram/table consistency, and links. Do not run application builds or tests for a documentation-only change.