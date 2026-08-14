# Phase 3 — Invariant extraction (the falsification engine)

This is the phase that finds bugs with no name. Do it in writing, before
writing any attack code.

## What an invariant is here

A security assumption the code **depends on but never re-checks**. The gap
between *where it is established* and *everywhere it is assumed* is the
hunting ground.

## How to extract them

For each subsystem, ask: "what must be true for this to be safe, and what
would see if it weren't?" Seeds that generalize:

- **Token/artifact separation**: what stops token type A being redeemed as
  type B? (audience? typ? key? store?) If two types share a signing key, the
  separation is one claim value — attack the claim binding.
- **Exactly-once**: which mechanism makes consumption atomic in EACH backend?
  (atomic INSERT / transaction isolation / single-thread). Is the mechanism
  equally strong in all of them?
- **Monotonic revocation**: can any sequence un-revoke? Can a first-use be
  sequenced so the legitimate party looks like the replay?
- **Cache authority**: is a cached decision re-validated per use, or does the
  cache confer authority the original check had but the reuse context lacks?
  Cross-mode/cross-tenant cache sharing is a confused-deputy seam.
- **Identity/ceiling semantics**: what differs between "absent", "empty", and
  "subset"? Does any path treat empty as absent (widening)?
- **Config-era isolation**: do records written under config generation N
  survive a switch to N+1? In both directions? Asymmetry is a finding.
- **Audit/observability is not a gate**: which call sites wrap the sink
  fail-open and which don't? An unwrapped sink turns IO failures into
  semantic changes (401→500 oracles, or worse).
- **Shared-secret binding**: if two deployments share a secret, what value
  actually binds a token to one deployment? Is that value collision-proof
  (path? issuer?) — and what about split-brain state (separate stores)?
- **Time/number edges**: boundary equality, fractional values, overflow at
  Date/safe-integer limits — does the accept/reject line move one tick?

## Output

Write `phase3-invariants.md` to the PoC workspace — Phase 4 and later
sessions reference it mechanically; if the file doesn't exist, the phase
isn't done. A numbered invariant list: for each, **where established** and
**where assumed**. Derive and number the Phase-4 hypotheses (H1…Hn) from
the gaps, each naming its invariant — Phase 4 attacks reference these IDs.
Invariants you can't construct an attack sequence for are still listed —
they document what was considered.
