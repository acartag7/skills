# Phase 4: seam falsification (level 3)

Attack the seams between individually-correct components, under
concurrency and partial failure. Every attack names the Phase 3 invariant
it targets.

## Seam patterns

Generate your own from the invariant list first. The patterns below are
seeds.

**Allocation rule, from field data.** When isolated components hold under
taxonomy testing, stop probing components deeper and shift budget to
compositions. Treat the system as a graph: the nodes are the things you
have proven work, and the edges are the untested combinations of them.
Findings concentrate at seams even when every component passes on its
own. A clean taxonomy sweep is the signal to go compositional, not to
declare safety.

**A. Multi-replica with a real store.** Stand up the real DB (Docker).
Two client instances, meaning two pools or connections, count as two
replicas sharing one schema.

- Race one single-use artifact across replicas. Expect exactly one
  winner, a distinct successor count of 1, and the correct post-state:
  the family or session stays alive or is revoked, per design.
- Invert the arrival order. Sequence an attacker's replay as the WINNER.
  Who dies: the attacker, or the legitimate client?
- Crash mid-transaction. Hold a row lock externally so the operation
  blocks inside its transaction, KILL the connection, release the lock,
  then verify there is no partial state, no false revocation, and a clean
  retry.
- **Find the placement fingerprint first** when testing multi-tenant
  isolation claims. Before asking "are neighbors isolated", find the
  co-location fingerprint the platform leaks: kernel cmdline tags, dmesg
  host identifiers, or hypervisor metadata. It turns "same-host adjacency
  untestable" into a boot-and-pair experiment. Pair two of your own
  instances on one host, then re-run the isolation probes at true
  co-location.

**B. Shared caches and single-flight.** Resolve one key concurrently:
expect one fetch and identical results. Wedge an in-flight slot and check
whether the cap fails closed and recovers after the deadline. Poison the
cache with huge, malformed, or duplicated freshness headers, and check
whether a cached decision is re-validated per use.

**C. Semantic seams.** Probe undefined against empty array against subset
for ceilings and policies, on EVERY path that consumes them, not just the
main one. Mix config eras on a shared store, in both directions.

**D. Cross-adapter differential, the sibling sweep of Phase 2 hits.**
This pattern generalizes Phase 2's parser and token sections; it is not a
re-run. Phase 2 probes the primary path with single requests. Phase 4 D
takes anything interesting from there and fires IDENTICAL raw requests at
every adapter, store, and mode, then diffs every response. Divergence
classes worth diffing: duplicate params (last-wins or reject), charset
content types, JSON bodies on form endpoints, repeated multi-value params
(checking whether an array gets stringified into a wider grant), and
status codes for identical rejections.

**E. Failure injection.** Use custom dependencies that throw
selectively:

- The sink throws on SUCCESS only. Is state orphaned? Is the client told
  the truth?
- The sink throws on FAILURE only. Does a rejection change status or
  semantics, for example a 401 becoming a 500, which is an oracle?
- The store dies between two steps of a flow. Can the operation be
  retried, or is the artifact burned? Fail-closed is good. Wedging is a
  finding.
- The limiter is down. Confirm the documented fail-open, then measure
  what that means. Unthrottled what, exactly?

**F. Split-brain state.** Run two instances with separate stores that
share secrets, issuer values, or paths. Does single-use still hold?
Usually NOT, and that is the finding. Check whether docs or boot guards
make the misconfiguration impossible, or merely discourage it.

**G. Unix-socket shadowing, to get a MITM position on a platform
channel.** Use this where a privileged daemon (an agent, a supervisor, a
CI runner) talks through a unix socket in user-writable or shared space.
RENAME the socket file. Do not delete it: the listener keeps serving from
the inode, and deletion watchers typically do not fire on a rename. Then
bind your own listener at the canonical path, and proxy both directions
to the renamed original. You now see, and can rewrite, every platform
request and response: signatures, tokens, protocol bodies, all from
attacker-equivalent context. Race note: if the daemon recreates the
socket, do the rename and the bind inside ONE process so there is no
window. Pair this with the Phase 2 signature-scope oracle. A captured
signature plus this position is arbitrary-body replay. The reverse
direction, forging responses the daemon consumes, turns audit and
metering records into attacker-controlled data. Check what the platform
records from responses it received through a user-positionable path.

**H. Lifecycle interlock between two components' state machines.** Where
two components handshake (driver against device model, client against
server session, agent against supervisor), run EVERY documented teardown
primitive of one against EVERY state of the other, and read the
counterpart's own diagnostics (kernel WARNs, service logs, status
registers) as the oracle. The rejected-handshake symptom is the
signature of a state machine that never modeled the teardown transition:
the peer performs its spec-conformant teardown, the counterpart refuses
half of it, then latches. Also enumerate the notification paths OUTSIDE
the guarded dispatch. Fast-path mechanisms (ioeventfds, signal fds,
webhook endpoints registered at setup) often bypass every status check
the main interface enforces, so a component that is "dead" by the main
interface's state may still be fully drivable through the side channel.
Firing a side-channel notification at a component whose main state says
inactive is a seam in itself. The hunting technique for this class is
the Phase 2 lifecycle-primitives entry. Phase 3 carries the seeds.

## Sequence dimensions (combine freely)

concurrency, partial failure, retries, replay, **restart**, replica
disagreement, stale caches, configuration transitions, store transitions,
timeout boundaries, **cancellation**, upstream against downstream
disagreement, and **clock moving backward**. A component correct in
isolation can compose incorrectly with another correct component. Attack
the assumption the composition silently requires.

## State-version transitions

Create state under config or version A, consume it under B, then reverse.
Security transitions are often asymmetric: one direction guarded, the
other silently allowed. The asymmetry is itself the finding.

## Evidence standard

- Real dependencies, or an explicit "unproven". Docker for databases.
  Raw sockets for header and param occurrence tests, because fetch
  clients hide differentials.
- If a runtime can't express the attack (the Fetch API coalesces
  duplicate headers), simulate what the real deployment layer delivers,
  and SAY SO. The harness patterns behind these (real stores in Docker,
  raw-socket clients, failure injection) are in `harness-playbook.md`.
- Run each attack twice: once to see it, once after fixing any harness
  doubt.
- Report the post-attack state, not just accept or reject.
