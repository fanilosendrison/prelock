# ADR-001: Return authoritative execution truth rather than a guaranteed domain outcome

- Status: Accepted
- Date: 2026-09-24

## Context

The bootstrap Product Intent establishes in Section 0.6 that an execution
occurrence has its own execution truth; that unknown or interrupted execution
truth must remain distinguishable from known non-execution and known success;
that `proto-runtime` does not promise universal exactly-once external effects;
and that the workflow owns the semantic response to an uncertain execution
outcome.

Section 0.7 established that `proto-runtime` must execute or cause execution of
workflow-requested mechanical work and return an authoritative outcome to the
governing workflow.

That phrasing admitted two materially different readings:

```text
reading A
→ proto-runtime must always establish an authoritative domain outcome
  for the requested effect

reading B
→ proto-runtime must return or preserve the authoritative execution
  truth available for the occurrence, which may itself be an unknown or
  unresolved outcome
```

Reading A conflicts with Section 0.6 when a mechanical occurrence may have
reached an external system but its actual outcome can no longer be
authoritatively observed, for example because the executing process disappeared
before the result was established.

The bootstrap Product Intent did not explicitly select between those readings.

Under `docs/repository-governance/prelock-discovery-classification.md`,
this was a `decision-required` condition: the Product Intent admitted multiple
materially different product semantics for one normative obligation, and no
accepted authority selected one.

The product owner has resolved that question.

## Decision

The mechanical-execution return obligation is:

> return or preserve the authoritative execution truth available for the
> occurrence, rather than always establish an authoritative domain outcome for
> the requested effect.

Unknown or unresolved outcome is itself valid execution truth when that is all
that can be authoritatively established.

The governing distinction is:

```text
proto-runtime
→ owns truthful generic execution truth

workflow
→ owns semantic interpretation and response
```

`proto-runtime` is responsible for representing truthfully what is
authoritatively known about execution, including that no authoritative outcome
could be established. It is not responsible for guaranteeing that a domain
outcome is always known.

The workflow owns the semantic response to unknown or unresolved execution
truth, for example:

```text
inspect authoritative external reality
retry safely
reconcile
block
request main-agent intervention
```

depending on the workflow's own semantics.

## What this decision does not weaken

This decision preserves the following existing Product Intent requirements:

* known success, known non-execution, and unknown or interrupted execution
  truth remain distinguishable;
* unknown execution truth must not be silently converted into success or
  non-execution;
* `proto-runtime` must preserve occurrence identity and known execution truth;
* the workflow owns retry, reconciliation, blocking, and related semantic
  response;
* `proto-runtime` does not promise universal exactly-once external effects.

## Consequences

The following consequences are accepted:

1. Section 0.7 requires truthfulness about execution rather than the guaranteed
   establishment of a domain outcome.

2. A mechanical occurrence whose external effect cannot be authoritatively
   observed may validly return execution truth equivalent to an unknown or
   unresolved outcome.

3. Unknown or unresolved execution truth is a truthful result, not a silent
   success and not a silent non-execution.

4. The workflow decides what an unknown or unresolved outcome means for its own
   progression.

5. `proto-runtime` must preserve enough generic execution truth for the
   workflow to distinguish known non-execution, known success, and unknown or
   interrupted truth; the representation of that truth remains undecided.

6. The Product Intent's prohibition on inferring effects that cannot be
   established remains in force.

## Non-decisions

This ADR does not decide:

* the representation of execution truth or any status vocabulary;
* whether an occurrence is retried, and by whom;
* retry, backoff, or escalation policy;
* reconciliation or compensation algorithms;
* external-state probing strategy;
* idempotency mechanisms;
* exactly-once or at-least-once semantics;
* crash-recovery protocol or durable persistence mechanism;
* result schemas or workflow-facing APIs;
* how the runtime detects that an outcome is no longer authoritatively known;
* which failures produce unknown execution truth;
* implementation architecture of any kind.

Those questions require separate derivation or explicit decisions.
