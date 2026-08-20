# Phase 3: invariant extraction (the falsification engine)

This is the phase that finds bugs with no name. Do it in writing, before
writing any attack code.

## What an invariant is here

A security assumption the code **depends on but never re-checks**. The gap
between *where it is established* and *everywhere it is assumed* is the
hunting ground.

## How to extract them

For each subsystem, ask: "what must be true for this to be safe, and what
would I see if it weren't?" Seeds that generalize:

- **Token and artifact separation.** What stops token type A from being
  redeemed as type B: audience, type header, key, or store? If two types
  share a signing key, the separation is one claim value. Attack the claim
  binding.
- **Exactly-once.** Which mechanism makes consumption atomic in EACH
  backend: an atomic INSERT, transaction isolation, or a single thread?
  Is the mechanism equally strong in all of them?
- **Monotonic revocation.** Can any sequence un-revoke? Can a first-use be
  sequenced so the legitimate party looks like the replay?
- **Cache authority.** Is a cached decision re-validated per use, or does
  the cache confer authority the original check had but the reuse context
  lacks? Cross-mode or cross-tenant cache sharing is a confused-deputy
  opening.
- **Identity and ceiling semantics.** What differs between "absent",
  "empty", and "subset"? Does any path treat empty as absent, widening the
  grant?
- **Config-era isolation.** Do records written under config generation N
  survive a switch to N+1? Test both directions. Asymmetry is a finding.
- **Audit is not a gate.** Which call sites wrap the audit sink fail-open
  and which don't? An unwrapped sink turns IO failures into semantic
  changes, such as 401 becoming 500, which is an oracle.
- **Shared-secret binding.** If two deployments share a secret, what value
  actually binds a token to one deployment? Is that value collision-proof?
  What happens under split-brain state, with separate stores?
- **Time and number edges.** Boundary equality, fractional values,
  overflow at Date and safe-integer limits. Does the accept or reject line
  move one tick?

## Output

Write `phase3-invariants.md` to the PoC workspace. Phase 4 and later
sessions reference it mechanically. If the file doesn't exist, the phase
isn't done. Produce a numbered invariant list. For each invariant, record
**where it is established** and **where it is assumed**. Derive the
Phase-4 hypotheses (H1 to Hn) from the gaps and number them, each naming
its invariant, because Phase 4 attacks reference these IDs. List the
invariants you can't construct an attack sequence for. They document what
was considered.
