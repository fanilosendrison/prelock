---
okf_version: "1.0"
kind: "KnowledgeAsset"
asset_type: "agent-directives"
domain: "proto-runtime"
severity: "strict"
name: "proto-runtime repository agent directives"
---

# proto-runtime repository directives

Use this file as the operational map for the `proto-runtime` repository.

The repository is currently in the product-definition phase and is
specification-first.

Do not infer implementation architecture, programming language, process
topology, persistence model, workflow syntax, or runtime mechanism from the
repository name, from `proto-go`, from `proto-ruu`, or from TURNLOCK.

## Product boundary

`proto-runtime` is defined by the Product Intent in:

```text
docs/specification/proto-runtime-spec.md
```

The product boundary is:

```text
workflow
    owns meaning and decisions

proto-runtime
    owns reusable execution continuity

execution environment
    realizes bounded requested work
```

`proto-runtime` is a reusable execution substrate for externally defined
workflows. It preserves and realizes workflow-owned progression across
execution boundaries.

It MUST remain workflow-agnostic.

Do not introduce runtime knowledge of workflow domain concepts such as:

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

Do not introduce runtime branches or hidden equivalents such as:

```text
if workflow == proto-go
if workflow == proto-ruu
```

If `proto-runtime` must understand the business meaning of a workflow's state
to execute it, the product boundary has been violated.

Caller-supplied child invocation input and child workflow results are
workflow-owned data. Do not let runtime semantics depend on, interpret,
validate, or transform their business meaning; the runtime carries them
between caller and child workflows without interpreting them.

Do not add first-class runtime semantics for generic workflow topology
(branching, iteration, retry, obligations, state machines, DAGs, joins,
scheduling) unless an accepted current Product Intent requirement cannot be
satisfied correctly through successive workflow-owned decisions across
existing runtime execution boundaries.

The first expected consumers are `proto-go` and `proto-ruu`. Neither product
defines `proto-runtime` semantics.

## Current phase and implementation prohibition

The repository is currently in:

```text
PRODUCT DEFINITION
```

The only currently authorized substantive product artifact is the accepted
Product Intent in:

```text
docs/specification/proto-runtime-spec.md
```

At the current repository state, do not create or select:

```text
source code
runtime code
schemas
public APIs
persistence mechanisms
workflow languages
executors
schedulers
databases
process supervisors
formal models
```

Do not settle any matter listed as an explicit non-decision in Section 4 of
the Product Intent, including:

```text
programming language
process topology
daemon versus command invocation
persistence technology
database
files versus event log
workflow artifact syntax
workflow API
workflow state serialization
runtime state serialization
mechanical executor implementation
main-agent harness integration mechanism
command protocol
IPC
locks or leases
crash-recovery algorithm
exact execution statuses
exact result schemas
package layout
workflow discovery
installation model
slash-command mapping
formal model
```

Implementation architecture MUST NOT be silently selected before explicit
derivation.

Missing product authority is a discovery, not implementation permission.

Do not skip from Product Intent directly to speculative implementation.

## Invariant discipline

One boundary invariant identity is explicitly admitted:
`PROTO-RUNTIME-INV-001 — Generic workflow topology remains workflow-owned`.

Do not create further invariant identifiers, an invariant set, or
invariant-shaped requirements unless a later explicit derivation task backed by
accepted authority establishes them.

## Authority by responsibility

1. `docs/specification/proto-runtime-spec.md`
   defines current normative product meaning.

2. Accepted ADRs under `docs/adr/`
   record explicit decision history and later amendments.

3. `docs/vision/proto-runtime-vision.md`
   is non-normative.

4. `docs/repository-governance/`
   owns repository procedure, not product meaning.

5. Future formal artifacts may check accepted semantics for their declared
   scope but do not replace normative Product Intent.

6. Future implementation and tests must conform to accepted authority and must
   not create missing product semantics.

Report inconsistencies between authoritative sources.

Do not silently choose the interpretation most convenient for implementation.

## Required reading

Before changing product semantics, deriving architecture, or preparing
implementation work, read:

1. `README.md`
2. `docs/specification/proto-runtime-spec.md`
3. `docs/adr/README.md`
4. `docs/adr/index.md`
5. `docs/repository-governance/proto-runtime-discovery-classification.md`
6. `docs/repository-governance/proto-runtime-engineering.md`

## Discovery handling

Every material discovery that affects product meaning must follow:

```text
docs/repository-governance/proto-runtime-discovery-classification.md
```

A missing semantic decision is not permission to improvise.

## ADR discipline

Do not create an ADR speculatively.

An ADR requires an actual identified decision beyond the initial accepted
Product Intent.

A semantic ADR requires explicit product-owner resolution.

An architecture ADR requires sufficient upstream semantics to constrain the
decision.

## Repository naming

Use lowercase kebab-case for ordinary new files and directories except standard
entry points such as:

```text
AGENTS.md
README.md
```

The repository directory name `proto-runtime` does not select a programming
language, runtime, or process model.

## Current target structure

The current authorized tree is:

```text
proto-runtime/
├── .gitignore
├── AGENTS.md
├── README.md
└── docs/
    ├── adr/
    │   ├── README.md
    │   └── index.md
    ├── repository-governance/
    │   ├── proto-runtime-discovery-classification.md
    │   └── proto-runtime-engineering.md
    ├── specification/
    │   └── proto-runtime-spec.md
    └── vision/
        └── proto-runtime-vision.md
```

Do not expand this structure merely because a likely future directory can be
anticipated.
