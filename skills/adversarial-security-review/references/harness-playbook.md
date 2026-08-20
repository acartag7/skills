# Harness playbook

## Workspace

```
/tmp/<repo>-poc/          # all PoCs, never inside the target repo
  races.mjs  confusion.mjs  cimd.mjs  proxy.mjs  failures.mjs  seams*.mjs
```

Prefer the target's **built `dist`** (via its package `exports` map) over
source `.ts`. Raw Node — even with `--experimental-strip-types` — cannot
resolve the *extensionless* relative imports most TS projects use, so
`await import("/abs/repo/src/x.ts")` dies at the first internal `./foo/bar`.
Import third-party deps via the repo's own node_modules path so versions match
the audited lockfile. See **Framework / library targets** below.

## Framework / library targets (vs applications)

An application already runs — start it and hit it. A framework/library does
not; you must construct a minimal host that exercises it, and the trust seam
often lives *between* its packages (server ↔ memory ↔ store adapters). This is
a distinct harness pattern:

- **Build the relevant packages first** (`dist/`), then import via the package
  `exports` map. For a workspace (pnpm/yarn), the PoC's own `node_modules` can't
  see workspace packages — symlink them in:
  `mkdir -p /tmp/<repo>-poc/node_modules/@scope && ln -sfn /abs/repo/packages/<pkg> /tmp/<repo>-poc/node_modules/@scope/<pkg>`
  for each package you touch. Now `import { X } from '@scope/pkg/subpath'`
  resolves through the real `exports` map, and the dist's own internal imports
  resolve from each package's `node_modules` (pnpm symlinks). Add the same
  symlink for shared transitive deps the PoC names directly (e.g. `zod`).
- **Construct the minimal host.** For a server framework: build the real app
  object (e.g. `createServer(app)`) and drive it in-process via `app.fetch(new Request(...))`
  — no port needed for logic PoCs; bind a real port only to observe socket/bind
  behavior. Supply the cheapest real dependencies (in-memory store) and state
  when a finding is store-agnostic-by-construction vs needs a real DB.
- **The seam is cross-package.** A decision made in the auth package, assumed by
  the server, consumed by the store, is the hunting ground. Compose the real
  packages; don't stub the boundary you're testing.

## Python targets

- Import the package **editable** from the repo (`sys.path.insert(0,
  "/abs/repo")` or `pip install -e`) so PoCs execute the audited source, not a
  stale installed wheel.
- Loopback "internal" target: `http.server`/`socketserver` bound to
  `127.0.0.1`, port 0; log every hit — **the hit log IS the SSRF evidence**.
  The parse may fail afterwards; the connection already happened.
- Dangerous parses (XML bombs, deep nesting): run in a `subprocess` with a
  wall-clock timeout so a vulnerable parse can't hang the session. macOS has
  no `timeout(1)` by default — don't wrap with it (this bites any process
  management in the PoC, not just dangerous parses).
- **Install-vs-constructed-query rubric** (frameworks with optional extras):
  install benign pure-Python deps to reach real execution; Docker for
  standable stores/DBs; constructed-query only for commercial/unavailable
  backends — and say so. The injectable string the real code builds is
  evidence of the code defect; live execution against the actual backend is
  what's unproven. A missing dep must never *silently* downgrade a finding's
  evidence level. Expect cascading optional deps (fix one import, the next
  fails); stop by finding value and label the residue blocked-by-harness.

## Raw-socket client (mandatory for header/param occurrence tests)

HTTP clients collapse exactly what you're hunting. Use `node:net` and write
the request text literally: duplicate header lines, absolute-form request
targets, hand-set Content-Length. Read until `close` with a destroy timeout.

## Real dependencies

- Docker for DBs: `docker run -d --name poc-db -e ... -p 13306:3306 mysql:8.4`.
  Wait for "ready for connections" in logs before testing. `docker rm -f` after.
- Two client instances (two pools/connections) = two replicas.
- External row lock (`SELECT ... FOR UPDATE` from a third connection) to
  freeze an operation mid-transaction; `KILL <id>` via PROCESSLIST to crash it.

## Evidence hierarchy (label every result with its level)

```
real production dependency          (live MySQL/Redis/IdP)
  > real implementation + controlled dependency   (Docker MySQL, stub transport)
    > faithful model                (simulated Fetch coalescing — SAY SO)
      > static reasoning            (design review — never report as "held")
```

All four are useful; they are not equivalent evidence. "SQLite held" never
proves a MySQL claim — write "SQLite held; MySQL unproven" instead.

## Harness honesty checklist

Before reporting ANY result — positive OR negative:
1. Echo the exact payload sent (did redaction/escaping corrupt it?).
2. Verify preconditions (the token was actually valid before tampering).
3. Check Content-Length, parser defaults, URL normalization, implicit
   redirects, header coalescing, retry behavior, connection reuse, mock
   fidelity, clock behavior, env vars, dependency versions, test-only
   config — and whether the request actually reached the path you think.
4. Before the word "held" enters the output: (a) assert every
   monkey-patch/proxy/stub you installed has an interception counter > 0 —
   an injection that never fired is indistinguishable from a defense that
   held; (b) assert every stub server (DNS/HTTP/…) answered a
   positive-control client of your own, in the same process, before the
   attack ran; (c) when injecting a dependency, run ONE call first and
   assert the returned shape against the port contract. (d) a byte-patching
   MITM counts as an instrument: dump and decode one full message FIRST and
   confirm your patch pattern exists in the plaintext on the wire, then
   count patch-firings — a patcher aimed at compressed or encrypted framing
   silently no-ops and manufactures "held" results.
5. **Exit-code capture binds to the target command, nothing else.** `$?`
   read after a pipe, a wrapper suffix, or a `; next-cmd` captures the LAST
   stage's exit, not the attack's. In wrapper functions, capture the
   command's exit into a variable FIRST, then format output. Print bodies
   explicitly — a probe that produced no body where one was expected is a
   failed probe, not a blocked one; never read "0" as success off a silent
   run.
6. **"Traffic reached X" is proven at X, not at the client.** For any
   reachability/exfiltration claim, the evidence is an observation at the
   destination (canary endpoint, listener log, server-side record) — never
   the client's exit code, which cannot distinguish "connected then reset"
   from "filtered before connect". Fire one positive-control hit to the
   canary from an unrestricted context before trusting zero-arrivals.
7. Re-run after fixing doubt. A 400 caused by YOUR bad Content-Length is not
   a finding — debug to ground truth. A caught false positive is evidence of
   a good assessment; record it as a disconfirmed hypothesis.
8. If a layer can't express the attack (Fetch API has no duplicate headers),
   simulate what the real runtime delivers and mark it.
9. **A later-found-blind instrument retroactively invalidates its results —
   helds included.** When you discover an instrument never fired (patcher on
   compressed frames, tracer that died at session start), every conclusion
   that depended on it converts from "held" to UNKNOWN, and any causal claim
   built on those runs is retracted explicitly in the record.

## Harness-lie gallery (self-inflicted false positives, all caught by re-running)

- `rg PATTERN dir1 dir2` with the paths in ONE quoted variable is a single
  nonexistent path — zero hits that read as "the code is clean". Empty
  results over a large repo are a harness bug until proven otherwise.
- `192.0.2.1` (TEST-NET-1) picked as the "public" IP in a DNS-rebind sim is
  classified private/reserved by `ipaddress` — the guard blocks your
  "passing" resolution. Use a genuinely public IP.
- A standalone validation call consumes the "public" resolution your
  rebinding counter alternates on — reset state between demonstrations.
- UNION-based SQLi with a column-count mismatch dies with a driver error that
  looks like a rejection — match the injected columns to the outer SELECT.
- A malicious filename joined to an absolute directory loses the leading `-`
  that made it an argv injection; the name must reach argv *relative*.
- A monkey-patch guard keyed on `FileHandle.path` silently no-ops — Node 24's
  fs/promises FileHandle has NO `.path`. Key poisons on a buffer/content
  marker or another stable identity, and assert the patch fired (checklist
  item 4).
- A hand-rolled DNS server that appends answer records but writes ANCOUNT=0
  makes c-ares drop every answer — every fetch fails instantly with a
  generic dns error that reads as "the guard rejected it". Set section
  counts AFTER building the message, and run a positive-control resolve
  through your own server before using it to prove a negative.
- A raw `dns.promises.Resolver` passes a loose structural check for a
  DnsResolver port but returns `string[]` where the port expects
  `{address, family}[]` — the fetcher rejects it as malformed and every
  probe "fails closed". Assert an injected dependency's returned shape
  against the port contract before building the attack on it.
- Error objects don't all carry `.code` (some expose it only via `.message`).
  Capture `e?.code ?? e?.message ?? String(e)` — a bare constructor name in
  your own output hides which check actually rejected the probe.
- A wrapper helper appending `2>/tmp/e; echo "exit=$?"` after a
  pipe-terminated command reports the LAST PIPE STAGE's exit (or the
  formatter's), not the attack command's — "exit=0" on a blocked request
  read as a firewall bypass. Bind exit capture to the target command and
  print the body before believing any status.
- An empty response body printed as nothing and a green exit number read as
  "data retrieved" — the request had failed and the emptiness went unnoticed.
  No body where a body was expected = instrument failed; debug the probe,
  not the target.
- A byte-patching proxy on a response channel whose frames are per-envelope
  gzip (or any compressed/encrypted framing) never matches plaintext
  patterns — two consecutive forgery passes "held" before one hexdump
  revealed the framing. Dump the wire before arming any patcher (checklist
  4d).
- Background instruments (tracers, proxies, canary servers) started inside a
  normal command session in a managed runtime are reaped when that session
  ends — the strace/proxy dies silently and later probes produce empty logs
  that look like target behavior. Start long-lived instruments detached per
  the platform's lifecycle contract, and assert the instrument is alive at
  readout time.
- Guest-side scripts embedded in host-language template literals are
  corrupted by escape processing (`\x00` becomes a real NUL byte, `\r\n`
  becomes line breaks, shell `${var:1}` breaks the host parser). Write
  guest scripts as standalone files and read them from disk; template
  escapes produce failures that masquerade as target errors.
- A reused scratch file (`/tmp/body`) shows the PREVIOUS probe's content
  after a failed fetch — stale bytes read as a response. Use per-probe
  filenames or truncate before every run.

## Concurrency patterns

- `Promise.all(Array.from({length: 16}, ...))` over the same artifact.
- Count winners, distinct outputs, AND inspect post-state.
- For wedge tests: controllable transport/store that hangs until an AbortSignal
  fires; confirm caps reject fast and recover after the deadline.

**Race honesty (hard rule):** sequential `await` then `await` is NOT a race —
it is a *sequencing* test, and must be labeled that way. Before the word
"race" enters the report, either (a) prove the interleaving is genuinely
non-deterministic (Promise.all with observed out-of-order completion), or
(b) force the contention externally (row-lock freeze + KILL, delayed
transport). A "race" that ran in program order overclaims; relabel it
"sequencing" or rerun it under the lock.

## Failure injection

Wrap ports/sinks: throw always / on success only / on failure only / between
step N and N+1 of a flow. The question is never "does it error" (it will) but
"what state does the NEXT operation observe".

Concrete patterns (the wrong one wastes an hour):
- **Patch the named method on the real interface**, e.g.
  `store.queryThreads = async () => { throw ... }`. Not a generic Proxy.
- **Avoid `new Proxy({}, { get: () => async () => { throw } })`** — it throws on
  *every* property access, including thenability/non-call probes
  (`Symbol.toPrimitive`, `.then`), producing unhandled rejections you can't
  attribute. If you must proxy, return a throwing function only when the
  accessed property is actually called, else `undefined`.
- **Find the real boundary first.** Handlers often query through an indirect
  adapter — `memory.storage.getStore('memory')` → a store object — not the
  obvious `memory.queryThreads`. Grep the handler for the actual call, then
  patch *that* (`memory.storage.getStore = async () => throwingStore`), or your
  injection silently no-ops and an empty result looks like "fail-closed" when
  it's really "patch didn't fire." Seed data first so an empty response is
  distinguishable from a real failure.

## Anti-patterns

- Don't substitute SQLite for a claim about MySQL.
- Don't report a rejection as "held" if your PoC sent malformed garbage.
- Don't embed real credentials in PoC files — PoCs get promoted to
  regression tests and attached to disclosures, which are published
  artifacts. Ephemeral/local-only creds, and grep for token shapes before
  any promote or attach.
- Don't mutate the target repo; don't commit PoCs; clean up containers.
