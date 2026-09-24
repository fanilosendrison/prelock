# proto-runtime Specification

> Working product specification derived from the current product discussion.
>
> This document records the initial Product Intent before invariants,
> architecture, or implementation mechanisms are derived or selected. Terms
> such as programming language, process topology, daemon versus command
> invocation, persistence technology, database, files versus event log,
> workflow artifact syntax, workflow API, workflow state serialization, runtime
> state serialization, mechanical executor implementation, main-agent harness
> integration mechanism, command protocol, IPC, locks or leases, crash-recovery
> algorithm, exact execution statuses, exact result schemas, package layout,
> workflow discovery, installation model, slash-command mapping, and formal
> model remain deliberately unspecified unless the product contract later
> requires them.

# 0. Product intent — governing product boundary

This section is normative for the current product direction. It states the
product outcome that later derivation exists to serve.

No invariant identifiers are admitted yet. No derived invariant set exists yet.
Deriving invariants from this Product Intent is separate, later work; see
Section 5.

## 0.1 Product definition

`proto-runtime` is a reusable execution substrate for externally defined
workflows.

It exists because several current proto-generation products need to execute
workflows that alternate between different execution contexts while preserving
correct continuation independently of conversational memory or transient
processes.

The first concrete consumers are expected to include:

* `proto-go`
* `proto-ruu`

Neither product defines `proto-runtime` semantics.

The runtime remains workflow-agnostic. It MUST NOT know product concepts such
as:

```text
ManagedContribution
READY FOR HANDOFF
validation obligation
Repository Publication Obligation
PUBLISHED
WorkBoundary
Git commit
Git branch
Git publication
worktree
```

Those concepts belong to workflows using the runtime.

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

The workflow decides what should happen.

`proto-runtime` makes the declared execution happen and preserves enough
execution truth for the workflow to continue correctly afterward.

`proto-runtime` MUST NOT invent workflow strategy, business semantics, or
undeclared progression merely because it is executing the workflow.

The governing statement is:

> **The workflow decides; proto-runtime preserves and realizes that decision
> across execution boundaries.**

## 0.2 Workflow-owned progression

The workflow remains authoritative for:

* the meaning of its state;
* its domain-specific facts and obligations;
* whether it can progress;
* what progression is semantically legal;
* what execution it requests next;
* how an execution outcome affects subsequent workflow progression;
* its semantic completion condition.

`proto-runtime` MUST NOT determine domain-specific progression on the
workflow's behalf.

For example, `proto-runtime` MUST NOT decide that:

```text
validation complete → READY
commit exists → publish
publication blocked → authored correction
```

unless those decisions have been made by the governing workflow.

The runtime may preserve, transport, identify, or return workflow-owned state
without understanding its meaning.

## 0.3 Workflow-independent runtime

A new workflow MUST be able to use `proto-runtime` without requiring
`proto-runtime` to be modified merely because that workflow introduces new
domain semantics.

Conceptually:

```text
proto-go ────┐
             │
proto-ruu ───┼──→ proto-runtime
             │
workflow X ──┘
```

Adding `workflow X` must not require runtime branches such as:

```text
if workflow == proto-go
if workflow == proto-ruu
```

or hidden equivalents based on their domain concepts.

If the runtime needs to understand the business meaning of a workflow's state
to execute it, the product boundary has been violated.

## 0.4 WorkflowExecution identity

One invocation of a workflow forms a logical `WorkflowExecution`.

A `WorkflowExecution` MUST have identity independent of:

* one conversational session;
* one main-agent process;
* one mechanical process;
* one operating-system process;
* one transient execution attempt.

Execution continuity MUST NOT depend on those transient contexts continuing to
exist.

The concrete identity representation is not selected by this Product Intent.

## 0.5 Stable governing workflow definition

An accepted `WorkflowExecution` must remain governed by a determinate workflow
definition.

An external modification to the workflow definition MUST NOT silently redefine
the semantics of an already-active execution.

This requirement does not yet select:

* hashes;
* commits;
* immutable artifacts;
* snapshots;
* package versions;
* workflow registries;
* source loading mechanisms.

It establishes only that correct continuation requires a stable governing
definition for an accepted execution.

## 0.6 Execution occurrences

A `WorkflowExecution` may require one or more bounded execution occurrences.

The runtime must distinguish the logical workflow execution from individual
attempts or occurrences used to realize requested work.

Conceptually:

```text
WorkflowExecution E
    ↓
execution occurrence O1
    ↓
workflow progresses
    ↓
execution occurrence O2
    ↓
...
```

An execution occurrence has its own execution truth.

If an occurrence was started but its outcome is no longer authoritatively
known, `proto-runtime` MUST NOT silently reinterpret that condition as:

```text
the occurrence never happened
```

or:

```text
the occurrence succeeded
```

Unknown or interrupted execution truth must remain distinguishable from known
non-execution and known success.

`proto-runtime` does NOT thereby promise universal exactly-once external
effects.

The workflow remains responsible for deciding the semantic response to an
uncertain execution outcome.

For example, a workflow may choose to:

```text
inspect authoritative external reality
retry safely
reconcile
block
request agentic intervention
```

depending on its own semantics.

## 0.7 Mechanical execution

A workflow may request mechanical execution.

Mechanical execution means that the requested work can be progressed according
to executable semantics and runtime inputs/results without requiring
discretionary main-agent judgment to invent missing workflow progression.

`proto-runtime` must be able to execute or cause execution of such work and
return an authoritative outcome to the governing workflow.

This Product Intent does NOT require:

* a particular script model;
* shell execution specifically;
* deterministic execution;
* one process model;
* one command format;
* one result schema.

A mechanical execution may itself interact with nondeterministic external
systems.

What matters is that the workflow has already decided to request that
execution.

## 0.8 Main-agent continuation

A workflow may request a continuation by an eligible main coding agent when
progress requires interactive agency rather than purely mechanical execution.

Conceptually:

```text
workflow owns progression
        ↓
requests main-agent continuation
        ↓
proto-runtime transfers control
        ↓
main agent performs bounded workflow-requested work
        ↓
continuation completes/yields
        ↓
proto-runtime returns control/result to workflow
```

A main-agent continuation MUST NOT make the main agent the hidden global
orchestrator of the workflow.

The workflow retains authority over what happens after that continuation.

The continuation may permit ordinary coding-agent behavior appropriate to the
available harness, including actions such as:

* repository inspection;
* reasoning;
* editing;
* testing;
* tool use;
* reacting to new evidence;
* user interaction when necessary.

`proto-runtime` does not currently require the strong TURNLOCK guarantee that
every main-agent continuation preserve one exact originating cognitive lineage.

Correct workflow execution MUST NOT depend on the originating conversational
session surviving.

If the originating session is unavailable, an eligible later main-agent context
may continue the execution when sufficient explicit continuation information
can be established.

The concrete harness and continuation-reconstruction mechanism remain
undecided.

## 0.9 Reversible and repeatable control transfer

A workflow may alternate repeatedly between workflow-owned progression and
execution contexts.

At minimum, `proto-runtime` must support shapes such as:

```text
workflow
→ mechanical execution
→ workflow
→ main-agent continuation
→ workflow
→ mechanical execution
→ workflow
→ complete
```

No one mechanical invocation or main-agent continuation owns the full workflow
merely because it temporarily owns control.

Correct continuation MUST NOT depend on a mechanical process remaining
suspended while main-agent work occurs.

## 0.10 Explicit execution continuity

Sufficient execution state must exist independently of conversational memory so
that an active `WorkflowExecution` can continue correctly after a control
boundary.

The governing principle is:

```text
workflow execution truth
!=
main-agent recollection
```

`proto-runtime` owns the generic execution facts required to resume control
correctly.

The workflow owns the semantic meaning of its own state.

This Product Intent does not yet decide the representation, persistence,
transport, reconstruction, or storage of either category.

## 0.11 Session-independent re-entry

An active workflow execution MUST NOT become semantically unrecoverable merely
because the session that initiated or previously advanced it no longer exists.

A later eligible session may re-enter an existing `WorkflowExecution` when the
execution can be authoritatively identified and sufficient continuation
information is available.

Re-entry MUST continue the existing logical workflow execution rather than
create a new one merely because a different session is now involved.

This requirement does not promise that every interrupted external operation can
automatically recover.

It promises preservation of the logical execution and its known execution
truth.

## 0.12 Control ownership

At any moment where one workflow execution has a continuation that must be
progressed by a main-agent controller, `proto-runtime` must prevent
contradictory independent progression of that same continuation.

The minimum product requirement is that one logical continuation must not be
independently consumed as though two unrelated controllers both exclusively
owned it.

This does not prohibit:

* multiple independent `WorkflowExecution` instances;
* workflow-defined execution that is internally concurrent if such a capability
  is later admitted;
* concurrency outside `proto-runtime`'s control.

The concrete locking, leasing, fencing, or ownership mechanism is undecided.

## 0.13 Multiple independent workflow executions

Distinct `WorkflowExecution` instances must be capable of existing and
progressing independently.

`proto-runtime` MUST NOT impose global serialization merely because another
workflow execution exists.

The current Product Intent does NOT require concurrency inside one workflow
execution.

In particular, this Product Intent does not yet derive:

```text
parallel workflow branches
fan-out
fan-in
joins
branch scheduling
```

unless a future product requirement establishes them.

Inter-execution concurrency and intra-execution workflow concurrency are
different problems.

## 0.14 Workflow invocation and structured return

A workflow may invoke another workflow as a child execution.

This capability is required because workflows such as `proto-go` may need to
call workflows such as `proto-ruu`.

Conceptually:

```text
WorkflowExecution E1
        │
        │ workflow call
        ▼
WorkflowExecution E2
        │
        │ child progression
        ▼
     outcome
        │
        ▼
return to E1's preserved continuation
```

The child workflow remains a distinct `WorkflowExecution`.

The parent must retain a return-bearing continuation while the child executes.

Normal child completion must return its outcome to the exact immediate caller
continuation rather than requiring the caller to reconstruct manually what
invoked the child.

The same workflow definition should be usable both:

```text
as a top-level workflow
```

and:

```text
as a child workflow invoked by another workflow
```

without acquiring domain-specific runtime behavior.

This requirement establishes only the minimum structured call/return semantics
required by current consumers.

It does NOT yet require:

* recursive workflow invocation;
* cyclic call graphs;
* arbitrary nested concurrency;
* delegated workflow-selection authority;
* workflow call graphs as a first-class authoring language;
* general-purpose stack inspection.

Those capabilities belong to later derivation if required.

## 0.15 Runtime versus workflow state

The product must preserve a conceptual distinction between:

```text
WORKFLOW STATE
```

and:

```text
RUNTIME EXECUTION STATE
```

Workflow state contains domain meaning owned by the workflow.

Runtime execution state contains generic execution facts required to preserve
control and continuation.

For example, `proto-runtime` may need to know conceptually:

```text
WorkflowExecution E1
governed by workflow definition D
waiting for child WorkflowExecution E2
```

while remaining completely ignorant that E1 is a proto-go publication
progression and E2 is performing Git versioning.

Likewise it may know:

```text
WorkflowExecution E2
waiting for mechanical occurrence O7
outcome currently unknown
```

without knowing that O7 concerns a Git push.

The concrete storage or representation boundary is not selected here.

## 0.16 Completion

A workflow determines its own semantic completion condition.

When the governing workflow authoritatively declares the `WorkflowExecution`
complete, `proto-runtime` must preserve that terminal execution fact and return
the workflow's result to its immediate caller where one exists.

`proto-runtime` MUST NOT infer workflow completion merely because:

* a process terminated;
* a main-agent session disappeared;
* a mechanical occurrence ended;
* no current process is running.

## 0.17 Product minimality

`proto-runtime` is deliberately smaller than TURNLOCK.

Its Product Intent is derived only from current proto-generation needs.

The presence of a capability in TURNLOCK is NOT sufficient justification for
adding that capability to `proto-runtime`.

Do NOT add, merely by analogy with TURNLOCK:

```text
raw LLM execution
independent-agent execution
fan-out / fan-in
parallel workflow topology
generic branching primitives
generic iteration primitives
workflow DAG interpretation
scheduler semantics
fairness guarantees
recursive workflows
cyclic workflow calls
workflow optimization
execution evaluation
provenance systems beyond current need
general-purpose workflow DSL
```

A workflow may implement branching, iteration, retry policy, obligation
processing, or domain state transitions inside its own progression logic
without `proto-runtime` understanding those structures.

The initial runtime should contain only capabilities that are forced by actual
current workflow requirements.

## 0.18 Governing product test

For every proposed `proto-runtime` capability, invariant, or architecture
decision, ask:

> **Can an externally defined workflow preserve ownership of its own semantics
> and progression while proto-runtime provides only the reusable execution
> continuity, control-transfer, execution-occurrence, and workflow-call
> machinery required to realize those decisions?**

A design is suspect if `proto-runtime` must understand why a workflow requested
an action.

A design is also suspect if every workflow must independently reimplement
session-independent continuation, execution occurrence identity, control
handoff, child-workflow return, or recovery of generic execution truth.

The desired boundary is:

```text
workflow
    owns meaning and decisions

proto-runtime
    owns reusable execution continuity

execution environment
    realizes bounded requested work
```

# 1. Purpose

Not yet derived.

Do not populate this section until an explicit derivation from the accepted
Product Intent establishes its contents.

# 2. Core mental model

The minimum product-level model currently established is:

```text
externally defined workflow
        ↓
WorkflowExecution
        ↓
execution occurrence(s)
        ├── mechanical execution
        └── main-agent continuation
        ↓
workflow-owned progression across control boundaries
        ↓
workflow-declared completion
```

Workflow-owned progression alternates with bounded execution contexts:

```text
workflow
→ mechanical execution
→ workflow
→ main-agent continuation
→ workflow
→ ...
```

A workflow may also invoke another workflow:

```text
WorkflowExecution E1
        │
        │ workflow call
        ▼
WorkflowExecution E2
        │
        │ child progression
        ▼
     outcome
        │
        ▼
return to E1's preserved continuation
```

The runtime preserves generic execution facts and control continuity. It does
not own the workflow's semantic state, business authority, or progression
decisions.

# 3. Product-Intent vocabulary

The following working meanings are used by Section 0. They are not a derived
canonical terminology, an invariant set, or an implementation contract.

## Workflow

An externally defined process that owns its own semantic state, business
authority, and progression decisions.

The authoring form of a workflow is not selected by this Product Intent.

## WorkflowExecution

One invocation of a workflow, forming the logical execution whose identity is
independent of conversational sessions, main-agent processes, mechanical
processes, operating-system processes, and transient execution attempts.

## Execution occurrence

A bounded execution used to realize requested work within a `WorkflowExecution`.

An execution occurrence is not equivalent to the logical `WorkflowExecution`.

## Execution truth

What is authoritatively known about an execution occurrence's realization.

The product requires unknown or interrupted execution truth to remain
distinguishable from known non-execution and known success. Exact status values
are not selected.

## Mechanical execution

Execution that can be progressed according to executable semantics and runtime
inputs/results without requiring discretionary main-agent judgment to invent
missing workflow progression.

## Main-agent continuation

A bounded control transfer to an eligible main coding agent so that
workflow-requested interactive work can be performed, after which control and
result return to the governing workflow.

## Continuation

The control position from which a workflow execution must subsequently proceed
after a control boundary.

The term also covers the return-bearing continuation preserved by an invoking
workflow while a child execution runs.

The representation of a continuation is not selected.

## Control boundary

A transition during which control moves between the workflow, mechanical
execution, and main-agent continuation, such that correct continuation must not
depend on the continuing existence of the context that previously held control.

## Stable governing workflow definition

The determinate workflow definition that governs an accepted `WorkflowExecution`
and must not be silently redefined by external modification while that
execution is active.

The concrete stability mechanism is not selected.

## Immediate caller continuation

The continuation belonging to the invoking workflow execution that must receive
a child execution's outcome.

## Eligible main-agent session

A main-agent session or context that is permitted to continue or re-enter a
`WorkflowExecution`.

The eligibility criteria are not selected.

## Workflow state

Semantic domain state owned by the workflow, whose meaning `proto-runtime` does
not need to understand.

## Runtime execution state

Generic execution facts owned by `proto-runtime` that are required to preserve
control and continuation.

# 4. Explicit non-decisions

The following matters are explicitly undecided. They are downstream
derivations and MUST NOT be silently selected by implementation, repository
layout, or adjacent product behavior:

* programming language;
* process topology;
* daemon versus command invocation;
* persistence technology;
* database;
* files versus event log;
* workflow artifact syntax;
* workflow API;
* workflow state serialization;
* runtime state serialization;
* mechanical executor implementation;
* main-agent harness integration mechanism;
* command protocol;
* IPC;
* locks or leases;
* crash-recovery algorithm;
* exact execution statuses;
* exact result schemas;
* package layout;
* workflow discovery;
* installation model;
* slash-command mapping;
* formal model.

This Product Intent also does not admit an invariant identifier space, an
authoring language, a workflow registry, a storage model, or an execution
status vocabulary.

No implementation is authorized by this Product Intent.

# 5. Derivation status

The repository is currently in the product-definition phase.

The following are not yet derived:

* the complete product model beyond Sections 0 and 2;
* the canonical terminology beyond the working vocabulary in Section 3;
* the invariant set and any invariant identifiers;
* the formal model and formal-assurance responsibilities;
* any implementation architecture;
* any implementation.

Resolving the following semantic questions requires explicit future derivation.
The list records the questions visible at this phase and is not exhaustive:

* How must a `WorkflowExecution` be authoritatively identified so that a later
  session re-enters the existing logical execution rather than creating a new
  one?
* What counts as sufficient explicit continuation information for
  session-independent re-entry, and how is sufficiency determined?
* What must be preserved as execution truth for an occurrence to count as
  authoritatively known, and how is unknown or interrupted truth distinguished
  from known non-execution and known success?
* What does an authoritative outcome returned by a mechanical occurrence mean
  when the occurrence interacts with nondeterministic external systems whose
  state `proto-runtime` cannot fully observe?
* What must remain determinate about a stable governing workflow definition
  given that the concrete stability mechanism is not selected?
* What must a workflow call and its structured return carry so that a child
  execution's outcome reaches the exact immediate caller continuation after
  arbitrary control boundaries?
* What makes a main-agent session or context eligible to continue or re-enter a
  `WorkflowExecution`?
* How is control ownership enforced without selecting a locking, leasing, or
  fencing mechanism?
* What representation boundary separates runtime execution state from
  workflow-owned state?

These questions remain unresolved. Missing product authority is a discovery,
not implementation permission.
