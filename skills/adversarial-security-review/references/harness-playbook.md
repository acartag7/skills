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
   assert the returned shape against the port contract.
5. Re-run after fixing doubt. A 400 caused by YOUR bad Content-Length is not
   a finding — debug to ground truth. A caught false positive is evidence of
   a good assessment; record it as a disconfirmed hypothesis.
6. If a layer can't express the attack (Fetch API has no duplicate headers),
   simulate what the real runtime delivers and mark it.

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
