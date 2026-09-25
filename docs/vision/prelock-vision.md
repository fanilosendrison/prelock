# Prelock Vision

> Non-normative direction. This document does not override the Product Intent,
> accepted ADRs, or future derived invariants.

Prelock is intended to become the reusable execution substrate of an
agentic software-development system whose workflows alternate between
workflow-owned semantic progression, mechanical execution, and main-agent
continuation.

Its long-term role is to let an externally defined workflow own its own
semantic state, business authority, and progression decisions while relying on
a shared substrate to preserve and realize that progression across execution
boundaries, including session loss, process termination, and replacement of
transient execution contexts.

The intended conceptual composition is:

```text
workflow
→ owns semantic state, business authority, and progression decisions

Prelock
→ preserves execution continuity
→ transfers control between execution contexts
→ tracks execution occurrences
→ carries workflow-call return structure

execution environment
→ realizes bounded requested work
```

`proto-go` and `proto-ruu` are expected first consumers.

A workflow using Prelock may alternate between mechanical execution and
main-agent continuation, and may invoke another workflow as a child execution
whose outcome returns to the immediate caller continuation.

Prelock admits only capabilities forced by accepted current product
requirements. Generic workflow topology remains workflow-owned: the runtime
realizes workflow-declared executions and returns their truth or result, and
the workflow decides again.

The initial repository deliberately remains specification-first.

The immediate work is to derive the invariant set, product model, and open
semantic decisions from the accepted Product Intent before selecting the
implementation architecture.

This document does not authorize implementation or architecture.
