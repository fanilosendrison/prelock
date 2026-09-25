# ADR-003: Keep generic workflow topology workflow-owned

- Status: Accepted
- Date: 2026-09-24

## Context

The accepted Product Intent establishes that the governing workflow owns
semantic state, business authority, and progression decisions, while
`proto-runtime` realizes workflow-requested execution and preserves execution
continuity.

That establishes what the runtime does. It did not yet establish an intrinsic
boundary preventing the runtime from accumulating first-class semantics for
generic workflow topology merely because such semantics are generally useful
workflow concepts.

Two readings remained compatible with the accepted Product Intent:

```text
reading A
→ the runtime may continue acquiring first-class generic orchestration
  semantics whenever that would make workflows more compact or convenient

reading B
→ generic workflow topology remains workflow-owned, and a first-class runtime
  capability requires an accepted current requirement that cannot be satisfied
  correctly by successive workflow-owned decisions across existing runtime
  execution boundaries
```

The Product Intent did not explicitly select between them.

Under `docs/repository-governance/prelock-discovery-classification.md`,
this was a `decision-required` condition.

The product owner has resolved it in favor of reading B.

## Decision

`proto-runtime` is an execution-continuity substrate, not a generic topology
execution system.

The governing workflow retains progression authority. `proto-runtime` realizes
individual workflow-declared execution requests and returns their execution
truth or result so that the workflow can decide again.

The fundamental progression shape is:

```text
workflow evaluates its authoritative state
        ↓
workflow decides the currently legal next progression
        ↓
workflow requests one execution
        ↓
proto-runtime realizes that execution
        ↓
execution truth / result becomes available
        ↓
workflow interprets it
        ↓
workflow decides again
        ↺
```

A workflow may produce behavior equivalent to sequencing, branching,
iteration, retry, conditional progression, obligation processing, or
state-machine transitions through repeated workflow-owned decisions across
runtime execution boundaries.

`proto-runtime` does not need first-class knowledge that successive decisions
collectively form a branch, loop, retry policy, state machine, obligation
graph, or other orchestration topology.

### Boundary

`proto-runtime` MUST NOT acquire first-class generic workflow-topology
semantics when the required behavior can be expressed by the governing
workflow retaining progression authority and making successive execution
requests across runtime boundaries.

Capabilities such as generic branching, iteration, retry,
obligation-processing, state-machine, DAG, fan-out/fan-in, join, scheduling, or
topology-interpretation primitives are not justified merely because they are
generally useful workflow concepts.

### Capability admission

Every first-class `proto-runtime` capability MUST be justified by an accepted
current Product Intent requirement that cannot be satisfied correctly by the
already admitted runtime capabilities while keeping progression semantics in
the workflow.

For every proposed capability, the governing test is conceptually:

```text
1. Which accepted current requirement forces this capability?

2. Can the requirement already be satisfied by:
   workflow decision
   → existing runtime execution
   → returned execution truth/result
   → next workflow decision?

3. If yes:
   the new first-class runtime capability is not justified.

4. If no:
   identify precisely what execution property is missing
   before admitting any new capability.
```

Convenience, elegance, expected future usefulness, and general workflow-engine
completeness are not sufficient reasons.

### General workflow completeness is not a product objective

`proto-runtime` is deliberately allowed to remain incomplete as a general
workflow system.

Its Product Intent does not require it to support every useful orchestration
structure. The runtime is complete relative to its accepted product
requirements, not relative to an abstract catalogue of workflow-engine
capabilities.

Therefore:

```text
absence of a generic capability
!=
product deficiency
```

when current accepted workflows can satisfy their requirements correctly
without that capability.

### Workflow-owned progression remains workflow-owned

Absent a future explicit product decision, the following remain workflow
responsibilities:

```text
which execution comes next
whether a condition causes one path or another
whether another iteration is required
whether an uncertain effect should be inspected or retried
whether an obligation remains outstanding
whether a result satisfies a business condition
whether progression should block
whether semantic completion has been reached
```

The runtime may preserve generic execution facts needed to realize and resume
those decisions. It does not take ownership of the decisions themselves.

### Child workflow invocation remains within the boundary

Child workflow invocation does not itself violate this boundary.

The runtime realizes one declared execution relationship: it creates or
continues the child `WorkflowExecution`, carries the caller-supplied
invocation input, preserves the parent continuation, and returns the child
result.

The parent workflow interprets the result and decides again.

The boundary does not authorize extrapolation to capabilities such as
concurrent child fan-out, arbitrary child joins, races, child retry policies,
recursive or cyclic call graphs, general call-stack semantics, or dynamic
workflow topology unless an accepted current requirement independently forces
them.

## Boundary invariant

The Product Owner authorizes one boundary invariant identity as part of this
decision, without beginning the general invariant derivation:

```text
PROTO-RUNTIME-INV-001 — Generic workflow topology remains workflow-owned
```

Its normative meaning:

> Generic workflow topology remains workflow-owned. `proto-runtime` MUST NOT
> acquire first-class semantics for generic workflow topology when the required
> progression can be correctly expressed by the governing workflow making
> successive decisions across existing runtime execution boundaries. A new
> first-class runtime capability MUST be forced by an accepted current Product
> Intent requirement that cannot be satisfied correctly through existing
> admitted runtime capabilities while preserving workflow-owned progression
> authority.

The invariant covers, without being limited to, branching, iteration, retry
policy, conditional progression, obligation processing, and generic topology
interpretation. It does not encode a finite feature blacklist.

## Relationship to existing Product Intent

This decision strengthens the Product Minimality section into a general
capability-admission property. It does not remove any capability already
justified by accepted current requirements.

Existing capabilities such as `WorkflowExecution` identity, stable governing
workflow definition, execution occurrences, truthful execution truth including
UNKNOWN, mechanical execution, main-agent continuation, repeatable control
transfer, explicit execution continuity, session-independent re-entry, control
ownership, multiple independent `WorkflowExecution` instances, child workflow
invocation, caller-supplied child invocation input, structured child return,
and workflow-declared completion remain justified by current requirements and
are not reopened by this decision.

ADR-001 and ADR-002 are unaffected.

No other invariant identities are introduced. The general invariant set remains
underived.

## Consequences

The following consequences are accepted:

1. Generic progression structure remains owned by the governing workflow.

2. The default composition model is workflow decides, runtime executes, and the
   workflow decides again.

3. A generally useful workflow capability is not by itself a justification for
   adding first-class runtime semantics.

4. A first-class runtime capability requires an accepted current requirement
   that the already admitted capabilities cannot satisfy correctly.

5. `proto-runtime` may remain incomplete as a general workflow system; that
   incompleteness is not a product deficiency when accepted workflows can
   satisfy their requirements without the missing capability.

6. Child workflow invocation and the existing admitted execution capabilities
   remain valid.

7. `PROTO-RUNTIME-INV-001` is the only admitted invariant identity.

## Non-decisions

This ADR does not decide:

* a workflow API or workflow syntax;
* a representation of branching, iteration, retry, state machines, or
  obligations;
* a scheduler or graph representation;
* persistence, event logs, or databases;
* execution queues or task systems;
* process topology or programming language;
* concurrency implementation;
* call-stack or continuation representation;
* whether any future capability should be admitted;
* the general invariant set.
