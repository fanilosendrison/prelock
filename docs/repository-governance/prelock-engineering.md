# Prelock Engineering Governance

This document governs repository work classification and authority boundaries.

It does not define Prelock product semantics.

## Authority

Use each source only for the responsibility it owns.

1. `docs/specification/prelock-spec.md`
   owns current normative product meaning.

2. Accepted ADRs under `docs/adr/`
   record explicit decision history and later amendments.

3. `docs/vision/prelock-vision.md`
   is non-normative motivation and long-term direction.

4. `docs/repository-governance/`
   owns repository procedure, not product meaning.

5. Future formal artifacts may verify derived properties for their declared
   scope but must not become independent Product Intent authority.

6. Future implementation and tests must conform to upstream product authority;
   they must not silently define missing semantics.

## Current repository phase

The repository is currently in:

```text
PRODUCT DEFINITION
```

The only currently authorized substantive product artifact is the accepted
Product Intent in:

```text
docs/specification/prelock-spec.md
```

No complete product model exists yet.

No canonical terminology has been derived yet.

No complete invariant set exists yet.

One boundary invariant identity is explicitly admitted.

No implementation architecture has been accepted yet.

No implementation language has been selected yet.

No process topology has been selected yet.

No daemon or command-invocation model has been selected yet.

No persistence mechanism has been selected yet.

No workflow artifact syntax has been selected yet.

No workflow API has been selected yet.

No workflow or runtime state serialization has been selected yet.

No mechanical executor has been selected yet.

No main-agent harness integration mechanism has been selected yet.

No command protocol or IPC has been selected yet.

No locking, leasing, or fencing mechanism has been selected yet.

No crash-recovery algorithm has been selected yet.

No exact execution status vocabulary has been selected yet.

No formal model has been established.

## Work classes

Classify repository work into these responsibility classes:

```text
Product Semantics
Formal Assurance
Architecture
Implementation
Qualification
Repository Governance
```

### Product Semantics

Use for work that establishes, derives, clarifies, or amends product meaning.

This includes:

* Product Intent changes;
* normative semantic decisions;
* canonical terminology;
* invariant derivation;
* semantic ADRs;
* specification changes.

### Formal Assurance

Use only after formal-assurance responsibilities have been explicitly
established.

Formal work checks accepted semantics.

It does not invent them.

### Architecture

Use only when upstream product semantics and invariants are sufficient to
constrain a mechanism-level design decision.

Architecture must not silently close a `decision-required` product question.

### Implementation

Use only to realize accepted architecture and semantics.

Implementation convenience never outranks Product Intent.

### Qualification

Use for evidence that an implementation satisfies the accepted product
contract.

Qualification does not define the contract.

### Repository Governance

Use for repository procedure, work management, artifact ownership, validation,
and contribution mechanics.

Repository governance does not define Prelock behavior.

## Current sequencing rule

Until further explicit product derivation occurs:

```text
Product Intent
→ semantic questions
→ explicit decisions where required
→ derived invariants
→ formal/assurance design as required
→ architecture
→ implementation
→ qualification
```

Do not skip from Product Intent directly to speculative implementation.

## Existing-project non-authority

The following projects may be consulted later as implementation context but are
not semantic authority for Prelock:

```text
proto-go
proto-ruu
Ruu
git-commits-push
dotagents
permission-enforcer
```

Do not copy their architecture into Prelock unless a later accepted
Prelock derivation independently requires it.

Prelock admits only capabilities forced by accepted current product
requirements. Generic workflow topology remains workflow-owned.
