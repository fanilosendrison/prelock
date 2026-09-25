# ADR-002: Carry caller-supplied child invocation input without interpreting it

- Status: Accepted
- Date: 2026-09-24

## Context

The Product Intent establishes workflow invocation with structured return: a
workflow may invoke another workflow as a child execution, the child remains a
distinct `WorkflowExecution`, the parent preserves a return-bearing
continuation, and normal child completion returns the child outcome to the
exact immediate caller continuation.

It did not explicitly establish that the caller must be able to supply the
workflow-owned invocation input required by the child execution.

Real current consumers make the requirement concrete. `proto-go` may invoke
`proto-ruu` and must supply:

```text
applicable WorkBoundary
+
applicable Git authority
```

`proto-ruu` explicitly relies on its caller to provide the work boundary and
authority it is allowed to operate on.

Under `docs/repository-governance/prelock-discovery-classification.md`,
this was a `decision-required` condition: the Product Intent admitted multiple
materially different readings about whether workflow calls carry
caller-supplied invocation input, and no accepted authority selected one.

The product owner has resolved it.

## Decision

A workflow invocation through `proto-runtime` must be capable of carrying the
caller-supplied, workflow-owned invocation input necessary to invoke the child
workflow.

The runtime owns the execution mechanics of carrying that input into the child
execution.

The runtime MUST NOT need to understand the business meaning of that input.

The governing relationship is:

```text
parent workflow
→ decides which child workflow to invoke
→ supplies the child invocation input it is authorized to provide

proto-runtime
→ creates/executes the child WorkflowExecution
→ preserves the association between the call, child execution, and parent continuation
→ makes the supplied invocation input available to the child
→ returns the child result to the exact immediate caller continuation

child workflow
→ interprets the supplied input according to its own semantics
→ owns its own progression
→ produces its own result
```

The three ownership layers are:

```text
CALLER WORKFLOW
→ selects/requests child invocation
→ supplies workflow-owned invocation input

proto-runtime
→ preserves call identity
→ creates/continues child execution
→ carries invocation input without interpreting it
→ preserves parent continuation
→ returns child result

CHILD WORKFLOW
→ interprets invocation input
→ owns child semantic progression
→ produces child result
```

The minimum generic structure of a workflow call is conceptually:

```text
workflow call
=
child workflow identity/definition
+
caller-supplied invocation input
+
child WorkflowExecution
+
preserved immediate caller continuation
+
child result returned to that continuation
```

This is semantic structure only. It does not define a concrete call object,
request schema, protocol, or data model.

### Input semantics

The supplied child invocation input is workflow-owned data.

`proto-runtime` must not require knowledge of its business semantics.

The input may contain whatever domain information and authority the child
workflow requires, subject to the caller's own authorization.

How authority is represented is not decided here.

`proto-runtime` must not be required to validate business authority
semantically. The runtime preserves only the generic execution relationship.
If authorization or domain validity must be interpreted, that belongs to the
workflows or other governing authorities.

### Result symmetry

The existing structured-return rule is preserved:

```text
child
→ result
→ exact immediate caller continuation
```

The same workflow-agnostic rule applies in both directions:

```text
caller → child:
opaque/workflow-owned invocation input

child → caller:
workflow-owned result
```

`proto-runtime` owns transport, correlation, and continuity. The workflows own
meaning.

Request/result symmetry in concrete representation is not required.

### Workflow-agnostic

This decision applies regardless of whether the child workflow is `proto-ruu`,
another proto workflow, or a future workflow. It introduces no special case for
any particular child workflow.

## Relationship to existing Product Intent

This ADR extends Section 0.14 without removing any existing assertion.

The child workflow remains a distinct `WorkflowExecution`; the parent retains
its exact return-bearing continuation; child completion still returns to that
continuation.

ADR-001 is unaffected. This decision concerns the invocation input carried into
the child execution, not the truthfulness of mechanical execution results.

No invariant identifiers are introduced. Deriving invariants remains separate,
later work.

## Consequences

The following consequences are accepted:

1. A workflow call may carry caller-supplied, workflow-owned invocation input.

2. `proto-runtime` carries and preserves that input without interpreting its
   business meaning and without validating its domain authority or validity.

3. The child workflow interprets the supplied input according to its own
   semantics and owns its own progression and result.

4. The generic call structure associates the child workflow
   identity/definition, the caller-supplied invocation input, the child
   `WorkflowExecution`, the preserved immediate caller continuation, and the
   returned child result.

5. The rule is workflow-agnostic and adds no proto-go- or proto-ruu-specific
   runtime behavior.

6. `WorkBoundary` and Git authority remain proto-go/proto-ruu domain concepts;
   they do not become proto-runtime concepts.

7. No invariant identity is created and no invariant set is derived.

## Non-decisions

This ADR does not decide:

* a workflow-call API;
* a workflow request schema;
* parameter lists;
* JSON;
* typed payloads;
* serialization;
* data transport;
* RPC;
* IPC;
* storage;
* a workflow registry;
* capability tokens;
* authorization token formats;
* schema validation;
* child-call retries;
* timeout semantics;
* cancellation semantics;
* recursion;
* cyclic calls;
* fan-out/fan-in;
* concurrent children;
* multiple simultaneous children;
* general call stacks;
* workflow DAGs;
* workflow DSLs;
* whether invocation input is copied, referenced, snapshotted, serialized,
  persisted separately, part of workflow state, part of a call record, or
  reconstructed;
* the exact relationship between supplied invocation input and child workflow
  state;
* the exact representation, identity, persistence, transport, recovery, and
  correlation of invocation input and child results.

Those remain downstream derivations.
