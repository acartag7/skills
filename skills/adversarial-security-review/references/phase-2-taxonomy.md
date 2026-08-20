# Phase 2: executed attack taxonomy

Goal: run the standard attacks as PoCs against the REAL code. Reading code
says "looks clean". Executing says "held". Only the second is evidence.

## Setup

- PoC workspace OUTSIDE the repo: `/tmp/<repo>-poc/*.mjs`.
- Import the target directly from a `.mjs`. Prefer the **built `dist`**
  through the package's `exports` map over source `.ts`. Many TypeScript
  projects use extensionless relative imports that raw Node cannot resolve,
  even with `--experimental-strip-types`, so `import('src/x.ts')` fails at
  the first internal import. For a pnpm or yarn workspace, symlink the
  workspace packages into the PoC's `node_modules` so bare `@scope/name`
  specifiers resolve. The dist's internal imports then resolve from each
  package's own `node_modules`. The full recipe and framework-versus-app
  guidance live in `harness-playbook.md`. Use the project's own
  node_modules for shared deps (jose, zod) so versions match.
- Prefer the real drivers, meaning framework adapters and store
  implementations, over mocks. Mock only network egress, and say so when
  you do.

## The taxonomy

Adapt to the target. Skip what doesn't apply, with a reason.

**Crypto and tokens.** alg=none. HS against RS confusion, using the public
key as the HMAC secret. Token-type confusion: replay every token type at
every other endpoint. Tampered claims with a stale signature.
Cross-audience, cross-issuer, and cross-resource replay, including two
configs sharing keys. Expiry boundaries: exp equals now, fractional
values, MAX_SAFE_INTEGER. Missing required claims.

**Signature-scope oracle.** One request, so run it on every signed channel.
Replay a captured valid signed request with a MODIFIED body. The error
delta tells you what the signature binds. A 401 means body changes break
it: the signature is body-bound. A 404 or other semantic error, such as
"no such object", means the signature still verified against your new
body. The signature covers the procedure or timestamp but not the body,
so capture-replay with arbitrary bodies is live. Find the capture
position with Phase 4 pattern G.

**Single-use artifacts.** Concurrent consume of one artifact, at least 8
ways, across auth codes, refresh tokens, nonces, JTIs, and CSRF or state
tokens. Run it per store backend. Then replay after the race: the replay
must be rejected and must trigger the theft response. Verify the winner's
successor state.

**Injection.** SQL injection in every store: check parameterization, but
also test weird values. NoSQL operator injection. Header injection through
user-controlled error strings, including CRLF and quote escapes. Log
injection. Template or HTML injection in rendered pages, meaning XSS in
every interpolated value.

**Parser differentials, raw sockets required.** Duplicate headers on the
security-relevant ones: Authorization, Origin, Content-Length.
Comma-coalesced against array-valued headers. Duplicate query params.
Duplicate form params, and note per adapter whether first wins, last
wins, or it rejects. Absolute-form request targets. Charset-suffixed
content types. A JSON body on form endpoints.

**SSRF and egress.** For every fetch the server can be made to perform:
IP encodings in decimal, hex, octal, and short form. IPv6 forms:
v4-mapped, NAT64, ULA, link-local. Userinfo pivots. Trailing dots. Case
tricks. Punycode. Redirects to internal targets. DNS rebinding, and
whether validate-then-connect pinning stops it. Cloud metadata IPs.
Dangerous ports.

**AuthZ semantics.** Scope and ceiling widening across undefined, empty
array, and subset. Cross-client credential replay. Privilege claims in
attacker-acceptable tokens. Step-up enforcement.

**LLM trust boundary.** Applies to agent frameworks, RAG, and tool-calling
apps. These attacks are stochastic, so run them as k-of-N evals, never
single-shot. Model output is untrusted input: does anything schema-validate
it before a tool runs, or does the route schema `z.unknown()` or `z.any()`
pass raw args to `execute`? Are untrusted tool *descriptions* retrieved
from a remote MCP server injected raw into the model context? Can they
override system instructions, or trigger a consequential tool call with no
approval boundary? Is retrieved content or persisted agent memory framed
as untrusted, or does it re-enter the prompt with provenance authority?
Does a tool-output validator actually run, or always succeed? Pin
temperature and seed, run N of at least 20, report the pass rate, and
judge with a model from a *different* family than the one under test.
The deterministic sub-parts, such as the route accepting `z.unknown()` or
the validator being a no-op, can and should be executed single-shot: they
are the load-bearing facts, and the k-of-N pass rate is the amplification
on top. If no model key is available, execute the deterministic parts and
mark the stochastic amplification **blocked-by-harness**, with the eval
design written out.

**Supply chain and fetched executable content.** Anywhere the product
fetches remote content and then executes, imports, or installs it: a
plugin, tool, or MCP-server registry. A template or codegen fetch. A
dynamic `import()` of a fetched URL. A package suggested by the model. Is
there subresource integrity, a pinned hash, or an allowlist before the
content runs, or is it fetched and then executed? A registry that pulls 30
third-party server configs with no pinning is a supply-chain RCE class,
distinct from egress SSRF, which only covers targeting.

## Rules

- **Re-execute before you report.** Every Phase-1 lead rated 7 or higher
  is re-run as a PoC here before it enters the report. Static review
  inflates severity on framework defaults and provider-defended paths.
  Expect at least one high-rated lead to fall, for example a "traversal"
  the default provider already clamps, or an SSRF form the URL normalizer
  defeats. Leads that don't reproduce go to a "downgraded-by-execution"
  lane with the specific reason, not to Confirmed.
- **Phase 2 owns single-request attacks against the PRIMARY path only.**
  Any hit, or any surprisingly lenient acceptance, becomes a Phase 4
  hypothesis: sweep it across every sibling under sequence conditions.
  Phase 2 finds the crack. Phase 4 finds whether the crack is systemic.
  Neither phase re-runs the other's work.
- State each hypothesis and its expected result before running. Report
  the observed result.
- N=16 for races is a good default. Count winners and distinct artifacts.
- After every race, check the post-state: is the family or session
  wedged, dead, or alive? The aftermath is part of the invariant.
- A rejection proves nothing if your PoC sent garbage. When an attack
  "passes", verify the PoC actually sent the real attack. Echo the
  payload.
- A rejection also proves nothing if the instrument never fired. Assert
  interception counts and positive-control responses before "held" enters
  the output. See the harness-playbook checklist, item 4.

## Output

Per attack: name, hypothesis, expected, observed, PoC filename. Findings
roll up to the report's Confirmed and Held sections.
