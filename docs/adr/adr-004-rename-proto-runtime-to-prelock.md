# ADR-004: Rename proto-runtime to Prelock

- Status: Accepted
- Date: 2026-09-25

## Decision

The product currently named `proto-runtime` is renamed `Prelock`.

The canonical lowercase repository identifier is `prelock`.

The rename mapping is:

```text
Former product name:
proto-runtime

Canonical product name:
Prelock

Former repository slug:
proto-runtime

Canonical repository slug:
prelock

Former repository:
fanilosendrison/proto-runtime

Canonical repository:
fanilosendrison/prelock

Former primary invariant identity:
PROTO-RUNTIME-INV-001

Canonical primary invariant identity:
PRELOCK-INV-001
```

The following decisions are established:

1. This is an identity rename only.
2. Product Intent is unchanged.
3. Product semantics are unchanged.
4. Architecture is unchanged.
5. Scope is unchanged.
6. Authority boundaries are unchanged.
7. Existing accepted ADR-001 through ADR-003 remain historically valid.
8. Historical prose may retain `proto-runtime` when referring to the product
   under the name it had when that decision was accepted.
9. Current canonical documentation must use `Prelock`.
10. Current repository/file/path references must use `prelock`.
11. `PROTO-RUNTIME-INV-001` is renamed `PRELOCK-INV-001`.
12. The invariant rename changes identity only; its semantic content is
    unchanged.
13. No new Prelock capability is established by this ADR.
