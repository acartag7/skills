# Harness playbook

## Workspace

```
/tmp/<repo>-poc/          # all PoCs, never inside the target repo
  races.mjs  confusion.mjs  cimd.mjs  proxy.mjs  failures.mjs  seams*.mjs
```

Prefer the target's **built `dist`**, reached through the package
`exports` map, over source `.ts`. Raw Node cannot resolve the
extensionless relative imports most TypeScript projects use, even with
`--experimental-strip-types`, so `import("/abs/repo/src/x.ts")` dies at
the first internal `./foo/bar`. Import third-party deps through the
repo's own node_modules path so versions match the audited lockfile. See
**Framework and library targets** below.

## Framework and library targets (versus applications)

An application already runs. Start it and hit it. A framework or library
does not run on its own, so you must construct a minimal host that
exercises it, and the trust boundary often lives *between* its packages:
server against memory against store adapters. This is a distinct harness
pattern:

- **Build the relevant packages first** (`dist/`), then import through
  the package `exports` map. For a pnpm or yarn workspace, the PoC's own
  `node_modules` can't see workspace packages, so symlink them in:
  `mkdir -p /tmp/<repo>-poc/node_modules/@scope && ln -sfn /abs/repo/packages/<pkg> /tmp/<repo>-poc/node_modules/@scope/<pkg>`
  for each package you touch. Now `import { X } from '@scope/pkg/subpath'`
  resolves through the real `exports` map, and the dist's internal
  imports resolve from each package's `node_modules` (pnpm symlinks). Add
  the same symlink for shared transitive deps the PoC names directly,
  for example `zod`.
- **Construct the minimal host.** For a server framework, build the real
  app object (for example `createServer(app)`) and drive it in-process
  through `app.fetch(new Request(...))`. No port is needed for logic
  PoCs; bind a real port only to observe socket or bind behavior. Supply
  the cheapest real dependencies, such as an in-memory store, and state
  when a finding is store-agnostic-by-construction versus when it needs
  a real DB.
- **The boundary is cross-package.** A decision made in the auth package,
  assumed by the server, and consumed by the store is the hunting
  ground. Compose the real packages. Don't stub the boundary you are
  testing.

## Python targets

- Import the package **editable** from the repo, with
  `sys.path.insert(0, "/abs/repo")` or `pip install -e`, so PoCs execute
  the audited source rather than a stale installed wheel.
- Loopback "internal" target: bind `http.server` or `socketserver` to
  `127.0.0.1` on port 0, and log every hit. The hit log IS the SSRF
  evidence. The parse may fail afterwards; the connection already
  happened.
- For dangerous parses such as XML bombs or deep nesting, run in a
  `subprocess` with a wall-clock timeout so a vulnerable parse can't hang
  the session. macOS has no `timeout(1)` by default, so don't wrap with
  it. This bites any process management in the PoC, not just dangerous
  parses.
- **Install-versus-constructed-query rubric**, for frameworks with
  optional extras: install benign pure-Python deps to reach real
  execution. Use Docker for standable stores and DBs. Use a constructed
  query only for commercial or unavailable backends, and say so. The
  injectable string the real code builds is evidence of the code defect;
  live execution against the actual backend is what's unproven. A
  missing dep must never silently downgrade a finding's evidence level.
  Expect cascading optional deps, where fixing one import fails on the
  next. Stop when you find value, and label the residue
  blocked-by-harness.

## Raw-socket client (mandatory for header and param occurrence tests)

HTTP clients collapse exactly what you're hunting. Use `node:net` and
write the request text literally: duplicate header lines, absolute-form
request targets, hand-set Content-Length. Read until `close`, with a
destroy timeout.

## Real dependencies

- Docker for DBs: `docker run -d --name poc-db -e ... -p 13306:3306 mysql:8.4`.
  Wait for "ready for connections" in the logs before testing. Run
  `docker rm -f` after.
- Two client instances, meaning two pools or connections, count as two
  replicas.
- To freeze an operation mid-transaction, hold an external row lock with
  `SELECT ... FOR UPDATE` from a third connection. To crash it, run
  `KILL <id>` through PROCESSLIST.

## Evidence hierarchy (label every result with its level)

```
real production dependency          (live MySQL, Redis, IdP)
  > real implementation + controlled dependency   (Docker MySQL, stub transport)
    > faithful model                (simulated Fetch coalescing, SAY SO)
      > static reasoning            (design review, never report as "held")
```

All four are useful, and they are not equivalent evidence. "SQLite held"
never proves a MySQL claim. Write "SQLite held; MySQL unproven" instead.

## Harness honesty checklist

Before reporting ANY result, positive or negative:

1. Echo the exact payload sent. Did redaction or escaping corrupt it?
2. Verify preconditions. The token must have been valid before you
   tampered with it.
3. Check Content-Length, parser defaults, URL normalization, implicit
   redirects, header coalescing, retry behavior, connection reuse, mock
   fidelity, clock behavior, env vars, dependency versions, test-only
   config, and whether the request actually reached the path you think.
4. Before the word "held" enters the output:
   (a) Assert every monkey-patch, proxy, or stub you installed has an
   interception counter above zero. An injection that never fired is
   indistinguishable from a defense that held.
   (b) Assert every stub server (DNS, HTTP) answered a positive-control
   client of your own, in the same process, before the attack ran.
   (c) When injecting a dependency, run ONE call first and assert the
   returned shape against the port contract.
   (d) A byte-patching proxy is also an instrument. Dump and decode one
   full message first, confirm your patch pattern exists in the
   plaintext on the wire, then count patch firings. A patcher aimed at
   compressed or encrypted framing silently no-ops and manufactures
   "held" results.
5. **Bind exit-code capture to the target command, nothing else.** `$?`
   read after a pipe, a wrapper suffix, or a following command captures
   the last stage's exit, not the attack's. In a wrapper function,
   capture the command's exit into a variable first, then format the
   output. Print bodies explicitly. A probe that produced no body where
   one was expected is a failed probe, not a blocked one. Never read "0"
   as success off a silent run.
6. **Prove "traffic reached X" at X, not at the client.** For any
   reachability or exfiltration claim, the evidence is an observation at
   the destination: a canary endpoint (a controlled listener or a webhook
   capture service), a listener log, or a server-side record. A client
   exit code cannot distinguish "connected then reset" from "filtered
   before connect". Put a unique marker string in the payload so you can
   grep the canary's log for it. Fire one positive-control hit from an
   unrestricted context before trusting zero arrivals, and one
   negative-control hit from a known-blocked path. The canary staying
   silent there is what validates the enforcement. Only the canary's
   log, never the client's exit code, decides whether traffic arrived.
7. Re-run after fixing doubt. A 400 caused by YOUR bad Content-Length is
   not a finding. Debug to ground truth. A caught false positive is
   evidence of a good assessment; record it as a disconfirmed
   hypothesis.
8. If a layer can't express the attack (the Fetch API has no duplicate
   headers), simulate what the real runtime delivers, and mark the
   simulation.
9. **A later-found-blind instrument retroactively invalidates its
   results, helds included.** When you discover an instrument never
   fired, for example a patcher on compressed frames or a tracer that
   died at session start, every conclusion that depended on it converts
   from "held" to UNKNOWN. Retract any causal claim built on those runs,
   explicitly, in the record.

## Harness-lie gallery (self-inflicted false positives, all caught by re-running)

- `rg PATTERN dir1 dir2` with the paths in ONE quoted variable is a
  single nonexistent path. Zero hits then read as "the code is clean".
  Empty results over a large repo are a harness bug until proven
  otherwise.
- `192.0.2.1` (TEST-NET-1) picked as the "public" IP in a DNS-rebind
  simulation is classified private or reserved by `ipaddress`. The guard
  blocks your "passing" resolution. Use a genuinely public IP.
- A standalone validation call consumes the "public" resolution your
  rebinding counter alternates on. Reset state between demonstrations.
- UNION-based SQL injection with a column-count mismatch dies with a
  driver error that looks like a rejection. Match the injected columns
  to the outer SELECT.
- A malicious filename joined to an absolute directory loses the leading
  `-` that made it an argv injection. The name must reach argv
  *relative*.
- A monkey-patch guard keyed on `FileHandle.path` silently no-ops,
  because Node 24's fs/promises FileHandle has no `.path`. Key poisons
  on a buffer, a content marker, or another stable identity, and assert
  the patch fired (checklist item 4).
- A hand-rolled DNS server that appends answer records but writes
  ANCOUNT=0 makes c-ares drop every answer. Every fetch then fails
  instantly with a generic DNS error that reads as "the guard rejected
  it". Set the section counts AFTER building the message, and run a
  positive-control resolve through your own server before using it to
  prove a negative.
- A raw `dns.promises.Resolver` passes a loose structural check for a
  DnsResolver port but returns `string[]` where the port expects
  `{address, family}[]`. The fetcher rejects it as malformed and every
  probe "fails closed". Assert an injected dependency's returned shape
  against the port contract before building the attack on it.
- Error objects don't all carry `.code`; some expose it only through
  `.message`. Capture `e?.code ?? e?.message ?? String(e)`. A bare
  constructor name in your own output hides which check actually
  rejected the probe.
- A wrapper helper that appends `2>/tmp/e; echo "exit=$?"` after a
  pipe-terminated command reports the last pipe stage's exit, or the
  formatter's, instead of the attack command's. One blocked request was
  read as a firewall bypass this way. Bind exit capture to the target
  command, and print the body before believing any status.
- An empty response body printed as nothing, next to a green exit
  number, was read as "data retrieved". The request had failed and the
  emptiness went unnoticed. No body where a body was expected means the
  instrument failed. Debug the probe, not the target.
- A byte-patching proxy on a response channel whose frames are
  per-envelope gzip never matches plaintext patterns. Two consecutive
  forgery passes were reported as "held" before one hexdump revealed the
  framing. Dump the wire before arming any patcher (checklist item 4d).
- Background instruments such as tracers, proxies, and canary servers,
  started inside a normal command session in a managed runtime, are
  reaped when that session ends. The strace or proxy dies silently, and
  later probes produce empty logs that look like target behavior. Start
  long-lived instruments detached, per the platform's lifecycle
  contract, and assert the instrument is alive at readout time.
- Guest-side scripts embedded in host-language template literals are
  corrupted by escape processing: `\x00` becomes a real NUL byte,
  `\r\n` becomes line breaks, and shell `${var:1}` breaks the host
  parser. Write guest scripts as standalone files and read them from
  disk. Template escapes produce failures that masquerade as target
  errors.
- A reused scratch file such as `/tmp/body` shows the PREVIOUS probe's
  content after a failed fetch. Stale bytes read as a response. Use
  per-probe filenames, or truncate before every run, and before citing
  an output file as this run's evidence, verify freshness with a
  timestamp or file-size check.
- **Environment plumbing.** An env var set in the shell but never read by
  the child process: a missing export, a wrong variable name, an
  `--env-file` override, or an ambient fallback silently supplying old
  credentials. Probe that the value the child sees is the value you set,
  one line, before any test that depends on credentials being present.
- **SDK arity.** An SDK create call took one options object. A second
  argument carrying different credentials was silently ignored, and both
  sandboxes were created under the first credential set. Check the
  actual signature in the installed package's type definitions before
  passing credentials through any SDK call.
- **Binary bytes in socket logs.** A proxy that relays raw HTTP writes
  gzip response bytes into its log, which crashes text-mode readers. In
  Python, open socket-level logs with `open(path, "rb")` and decode each
  line as latin1, which never throws on raw bytes, or base64-encode
  before writing.
- **Stacked instrument closes.** One surface read as unreachable through
  three independent probe bugs before a fourth mechanism opened it.
  `pread()` on `/dev/mem` returned zeros for MMIO, because the kernel
  copy path never touches the mapping. Zeros are NOT the device's
  answer. A register that must hold a magic value reading zero convicts
  the instrument, not the target. A wide slice-copy (`m[:N]`, KB-range)
  of the MMIO mapping died with SIGILL inside an IFUNC-selected libc
  routine the masked vCPU didn't support, which presents exactly like
  "access forbidden". A ctypes-raw mmap EINVAL'd where the language's
  own mmap module succeeded, a marshaling bug. Every individual close
  looked airtight. Vary the mechanism AND the access width (narrow
  fixed-width reads are the fallback) before writing off a surface, and
  treat any accidental success on a "closed" surface as the
  highest-priority anomaly in the engagement.
- **dmesg is per-boot.** A "no trap was logged" conclusion was drawn from
  a recycled instance. The SIGILL traps had been logged all along, in
  the boot that died. Kernel log evidence must be read in the same boot
  as the event. When a probe process dies to a signal, grep dmesg
  IMMEDIATELY, in the same session, before anything recycles.
- **Unprivileged-probing false-denieds.** Each of these produced a
  "permission denied" or silent no-op that read as target enforcement:
  `os.path.getsize()` returns 0 for block devices (use lseek SEEK_END);
  python has no `os.mount` (raw syscall via ctypes, and note `mount(8)`
  checks real uid, not capabilities, so the helper refuses where the
  syscall succeeds); buffered `open(path, "w")` on sysfs attribute files
  raises ENOENT where `os.open(O_WRONLY)` works; `mmap.size()` EBADFs on
  anonymous mappings; `mmap.slice` assignments demand exact length, so a
  mis-packed struct raises an IndexError that looks like a mapping
  failure; `Atomics.wait` behaves differently across runtimes, so verify
  the sleep actually slept before trusting a timeout loop's "NEVER".
  Pack structs against the wire format's own header, not from memory: a
  20-byte pack where the format is 16 kills every downstream probe
  silently.
- **Verify the persistence carrier exists in YOUR namespace before
  designing evidence around it.** A log-to-persistent-disk design failed
  three times because the mount existed in dmesg (the kernel mounted it,
  in the init namespace) but never appeared inside the container's
  namespace, and first boots never mounted it at all. Probe the carrier
  with a write-and-reread in the same context that will rely on it
  BEFORE building the experiment on top.

## Marker-page observatory (for memory-write claims)

When the hypothesis is "component B writes into memory owned by A at
attacker-influenced addresses," don't poll for abstract effects. Build a
page observatory: mmap one page MAP_POPULATE, `mlock` it, read its GPA
via `/proc/self/pagemap`, fill it with a sentinel byte, and poll it from
a detached process that logs the first difference with offset and bytes.
Point the hypothesized write at that GPA. A mutated sentinel is direct
evidence of the write primitive. A quiet sentinel after a POSITIVE
control (any known writer, even your own second process writing to the
GPA via /dev/mem if the platform allows) validates the observatory.
Keep the observatory in a DETACHED process when the trigger runs in a
killable command session, and remember its anonymous mappings die with
it. Anything the experiment needs ALIVE at trigger time (sprays, holders)
must live in the SAME process as the trigger, or be re-established by it.

## Split-phase banking when the sequence may kill the session

If a manipulation can terminate the command stream (target crash,
recycle, watchdog), never put the whole experiment in one command.
Stdout dies with it, and the only evidence is an exception. Split the
sequence so each phase is its own command. Each phase's return banks its
output, and the phase whose return never arrives IS the attribution.
This doubles as kill-attribution: setup-and-arm in phase 1, the
dangerous trigger alone in phase 2, observation in phase 3. Emit
per-phase progress lines as steps complete (stderr/console.error, not
only a final dump) so long batteries are inspectable mid-flight and
their partial results survive the process dying at any step.

## Reaction-attribution control

When the observed effect looks like the platform reacting to you (a
reboot, a recycle, a destroy, a rate-limit), run the identical flow minus
your manipulation as an A/B control. The reaction may be the platform's
routine lifecycle behavior, not your doing. Then inventory every actor
that touches the environment before attributing the reaction to the
target, including the operator's own automation. One "platform destroys
wedged instances within seconds" claim stood for an hour before the
operator's own cleanup cron turned out to be the destroyer. The control
run and the actor inventory together are what make a reaction claim
attributable.

## Detached instrumentation on managed sandboxes

When the test environment kills background processes (idle hibernation,
sandbox timeouts), long-running instrumentation such as a proxy, MITM, or
logger must run detached inside the sandbox, not in a command the SDK
waits on:

1. Write every script (proxy, orchestrator, attack) to disk with
   fs.writeFile, never embedded in template literals. See the gallery
   entry on guest-side scripts.
2. Start the orchestrator detached: `nohup script.sh &` in its own exec.
3. Trigger the test. This is the only parent-side exec.
4. Read results from files after the orchestrator signals completion.

The parent never sends a command through a socket the proxy is
intercepting at teardown time. That race closes the exec channel.

## Concurrency patterns

- `Promise.all(Array.from({length: 16}, ...))` over the same artifact.
- Count winners and distinct outputs, and inspect the post-state.
- For wedge tests, use a controllable transport or store that hangs
  until an AbortSignal fires. Confirm caps reject fast and recover after
  the deadline.

**Race honesty, a hard rule.** Sequential `await` then `await` is NOT a
race. It is a *sequencing* test, and must be labeled that way. Before
the word "race" enters the report, either prove the interleaving is
genuinely non-deterministic (Promise.all with observed out-of-order
completion), or force the contention externally (row-lock freeze plus
KILL, or a delayed transport). A "race" that ran in program order
overclaims. Relabel it "sequencing", or rerun it under the lock.

## Failure injection

Wrap ports and sinks so they throw always, on success only, on failure
only, or between step N and step N+1 of a flow. The question is never
"does it error", because it will. The question is "what state does the
NEXT operation observe".

Concrete patterns, since the wrong one wastes an hour:

- **Patch the named method on the real interface**, for example
  `store.queryThreads = async () => { throw ... }`. Not a generic Proxy.
- **Avoid `new Proxy({}, { get: () => async () => { throw } })`.** It
  throws on *every* property access, including thenability and non-call
  probes (`Symbol.toPrimitive`, `.then`), producing unhandled rejections
  you can't attribute. If you must proxy, return a throwing function
  only when the accessed property is actually called. Otherwise return
  `undefined`.
- **Find the real boundary first.** Handlers often query through an
  indirect adapter, for example `memory.storage.getStore('memory')`
  returning a store object, rather than the obvious
  `memory.queryThreads`. Grep the handler for the actual call, then patch
  *that*, such as
  `memory.storage.getStore = async () => throwingStore`. Otherwise your
  injection silently no-ops, and an empty result looks like
  "fail-closed" when it is really "patch didn't fire". Seed data first,
  so an empty response is distinguishable from a real failure.

## Anti-patterns

- Don't substitute SQLite for a claim about MySQL.
- Don't report a rejection as "held" if your PoC sent malformed garbage.
- Don't embed real credentials in PoC files. PoCs get promoted to
  regression tests and attached to disclosures, which are published
  artifacts. Use ephemeral or local-only credentials, and grep for token
  shapes before any promotion or attachment.
- Don't mutate the target repo. Don't commit PoCs. Clean up containers.
