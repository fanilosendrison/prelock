# proto-runtime

`proto-runtime` is a reusable execution substrate for externally defined
workflows.

It exists so that an externally defined workflow can own its own semantic
progression while relying on a reusable execution substrate to preserve and
realize that progression across execution boundaries.

The governing separation is:

```text
workflow
= semantic state
+ business authority
+ progression decisions

proto-runtime
= execution continuity
+ control transfer
+ execution occurrence tracking
+ workflow-call return structure
```

The workflow decides what should happen. `proto-runtime` makes the declared
execution happen and preserves enough execution truth for the workflow to
continue correctly afterward.

The governing statement is:

> **The workflow decides; proto-runtime preserves and realizes that decision
> across execution boundaries.**

## Product boundary

```text
workflow
    owns meaning and decisions

proto-runtime
    owns reusable execution continuity

execution environment
    realizes bounded requested work
```

`proto-runtime` is workflow-agnostic. It MUST NOT know product concepts such as
`ManagedContribution`, `READY FOR HANDOFF`, validation obligations, Repository
Publication Obligations, `PUBLISHED`, `WorkBoundary`, Git commits, Git branches,
Git publication, or worktrees. Those concepts belong to workflows using the
runtime.

The first concrete consumers are expected to include:

```text
proto-go ────┐
             │
proto-ruu ───┼──→ proto-runtime
             │
workflow X ──┘
```

Neither `proto-go` nor `proto-ruu` defines `proto-runtime` semantics. Adding a
new workflow must not require `proto-runtime` to be modified merely because that
workflow introduces new domain semantics.

## What the runtime provides

The Product Intent requires `proto-runtime` to provide reusable execution
continuity independently of conversational memory and transient processes.

At the product level, that currently includes:

* logical `WorkflowExecution` identity independent of sessions, processes, and
  attempts;
* a stable governing workflow definition for accepted executions;
* bounded execution occurrences whose execution truth remains known or
  explicitly unknown;
* mechanical execution of workflow-requested work, returning the authoritative
  execution truth available for the occurrence;
* main-agent continuation when interactive agency is required;
* reversible and repeatable control transfer between those contexts;
* session-independent re-entry into an active execution;
* control ownership preventing contradictory independent progression of one
  continuation;
* independent progress of distinct workflow executions;
* workflow invocation with structured return to the immediate caller
  continuation;
* workflow-declared completion preserved as a terminal execution fact.

This list summarizes the Product Intent. The normative text remains
[`docs/specification/proto-runtime-spec.md`](docs/specification/proto-runtime-spec.md).

## Repository status

The repository is currently in the product-definition phase.

No implementation architecture, programming language, runtime, persistence
mechanism, workflow language, executor, scheduler, database, process supervisor,
protocol, or formal model is established merely by this repository layout.

Its authoritative starting point is:

* [`docs/specification/proto-runtime-spec.md`](docs/specification/proto-runtime-spec.md) — normative Product Intent;
* [`docs/adr/`](docs/adr/) — accepted decision history and later amendments;
* [`docs/vision/proto-runtime-vision.md`](docs/vision/proto-runtime-vision.md) — non-normative motivation and direction;
* [`docs/repository-governance/`](docs/repository-governance/) — repository procedure and engineering governance.

ADR-001 has been accepted. No invariant identifiers have been admitted yet.

Implementation architecture must not be silently selected before explicit
derivation from accepted Product Intent.
