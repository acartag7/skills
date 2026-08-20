# Phase 4 — Seam falsification (level 3)

Attack the seams between individually-correct components, under concurrency
and partial failure. Every attack names the Phase 3 invariant it targets.

## Seam patterns (generate your own from the invariant list first)

**Allocation rule (empirical):** when isolated components hold under
taxonomy testing, stop probing components deeper and shift budget to
compositions — edges between VERIFIED primitives (nodes = things you have
proven work, edges = untested combinations of them). Findings concentrate
at seams even when every component individually passes; a clean taxonomy
sweep is the signal to go compositional, not to declare safety.

**A. Multi-replica / real store.** Stand up the real DB (Docker). Two client
instances = two "replicas" sharing one schema.
- Race one single-use artifact across replicas: exactly one winner, distinct
  successor count = 1, correct post-state (family/session alive or revoked
  per design).
- Arrival-order inversion: sequence an attacker's replay as the WINNER — who
  dies, the attacker or the legit client?
- Crash mid-transaction: hold a row lock externally so the operation blocks
  inside its transaction, KILL the connection, release, then verify: no
  partial state, no false revocation, clean retry.
- **Placement fingerprint first** (multi-tenant isolation claims): before
  testing "are neighbors isolated", find the co-location fingerprint the
  platform leaks (kernel cmdline tags, dmesg host identifiers, hypervisor
  metadata) — it turns "same-host adjacency untestable" into a
  boot-and-pair experiment. Pair two of your own instances on one host and
  re-run the isolation probes at true co-location.

**B. Shared caches & single-flight.** Concurrent resolves of one key: one
fetch, identical results. Wedged in-flight slots: does the cap fail closed
and recover after the deadline? Cache-poison probes: huge/malformed/duplicated
freshness headers; does a cached decision get re-validated per use?

**C. Semantic seams.** undefined vs [] vs subset for ceilings/policies, on
EVERY path that consumes them (not just the main one). Config-era mixing on a
shared store, both directions.

**D. Cross-adapter/backend differential (the sibling sweep of Phase 2 hits).**
This pattern is the *generalization* of Phase 2's parser/token sections, not
a re-run: Phase 2 probes the primary path with single requests; Phase 4 D
takes anything interesting there and fires IDENTICAL raw requests at every
adapter/store/mode, then diffs every response. Divergence classes worth
diffing: duplicate params (last-wins vs reject), charset content-types,
JSON-on-form endpoints, repeated multi-value params (does an array get
stringified into a wider grant?), status codes for identical rejections.

**E. Failure injection.** Custom dependencies that throw selectively:
- sink throws on SUCCESS only → is state orphaned? is the client told the truth?
- sink throws on FAILURE only → does a rejection change status/semantics
  (401→500 oracle)?
- store dies between two steps of a flow → can the operation be retried, or
  is the artifact burned? fail-closed is good; wedging is a finding.
- limiter down → confirm documented fail-open, then measure what that means
  (unthrottled what?).

**F. Split-brain state.** Two instances with separate stores sharing
secrets/issuer/paths: does single-use still hold? (Usually NOT — that's the
finding. Check whether docs/boot guards make the misconfiguration
impossible or merely discouraged.)

**G. Unix-socket shadowing (acquiring a MITM position on a platform
channel).** Where a privileged daemon (agent, supervisor, CI runner) talks
through a unix socket in user-writable or shared space: RENAME the socket
file (do not delete — the listener keeps serving from the inode, and
deletion-watchers typically do not fire on rename), then bind your own
listener at the canonical path and proxy both directions to the renamed
original. You now see (and can rewrite) every platform request and response
— signatures, tokens, protocol bodies — from attacker-equivalent context.
Race note: if the daemon recreates the socket, do rename+bind inside ONE
process so there is no window. Pair with the Phase-2 signature-scope oracle:
a captured signature plus this position is arbitrary-body replay. The
reverse direction (forging responses the daemon consumes) turns audit and
metering records into attacker-controlled data — check what the platform
records from responses it received through a user-positionable path.

## Sequence dimensions (combine freely)

concurrency · partial failure · retries · replay · **restart** · replica
disagreement · stale caches · configuration transitions · store transitions ·
timeout boundaries · **cancellation** · upstream/downstream disagreement ·
**clock moving backward**. A component correct in isolation can compose
incorrectly with another correct component — attack the assumption the
composition silently requires.

## State-version transitions

Create state under config/version A, consume under B, then reverse.
Security transitions are often asymmetric (one direction guarded, the other
silently allowed) — asymmetry is itself the finding.

## Evidence standard

- Real dependencies or explicit "unproven". Docker for databases; raw sockets
  for header/param occurrence tests (fetch/clients hide differentials).
- If a runtime can't express the attack (e.g. Fetch API coalesces duplicate
  headers), simulate what the real deployment layer delivers and SAY SO.
- Run each attack twice: once to see it, once after fixing any harness doubt.
- Report the post-attack state, not just accept/reject.
