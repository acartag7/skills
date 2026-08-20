# Phase 2 — Executed attack taxonomy

Goal: run the standard attacks as PoCs against the REAL code. Reading code
says "looks clean"; executing says "held". Only the second is evidence.

## Setup

- PoC workspace OUTSIDE the repo: `/tmp/<repo>-poc/*.mjs`.
- Import the target directly from a `.mjs`. Prefer the **built `dist`** via the
  package's `exports` map over source `.ts` — many TS projects use *extensionless*
  relative imports that raw Node (even with `--experimental-strip-types`) cannot
  resolve, so `import('src/x.ts')` fails at the first internal import. For a
  pnpm/yarn workspace, symlink the workspace packages into the PoC's
  `node_modules` so bare `@scope/name` specifiers resolve; the dist's own
  internal imports then resolve from each package's `node_modules`. Full recipe
  + framework-vs-app guidance: `harness-playbook.md`. Use the project's own
  node_modules for shared deps (jose, zod, etc.) so versions match.
- Prefer the real drivers (framework adapters, store implementations) over
  mocks. Mock only network egress, and say so when you do.

## The taxonomy (adapt to the target; skip what doesn't apply with a reason)

**Crypto/tokens:** alg=none; HS↔RS confusion using the public key as HMAC
secret; token-type confusion (every token type replayed at every other
endpoint); tampered claims with stale signature; cross-audience/cross-issuer/
cross-resource replay, including two configs sharing keys; expiry boundaries
(exp==now, fractional, MAX_SAFE_INTEGER); missing required claims.
**Signature-scope oracle** (one request, run it on every signed channel):
replay a captured valid signed request with a MODIFIED body — the error
delta tells you what the signature binds. `401/unauthenticated` = body
changes break it (body-bound); `404/semantic error` ("no such object") =
the signature still verified against your new body (signature covers
procedure/endpoint/timestamp but NOT the body) → capture-replay with
arbitrary bodies is live; find the capture position (see Phase 4 pattern G).

**Single-use artifacts:** N-way (≥8) concurrent consume of: auth codes,
refresh tokens, nonces/JTIs, CSRF/state tokens — per store backend. Then:
replay AFTER the race (must be rejected AND trigger the theft response);
verify the winner's successor state.

**Injection:** SQLi in every store (check parameterization, but also test
weird values); NoSQL operator injection; header injection via user-controlled
error strings (CRLF, quote escape); log injection; template/HTML injection in
rendered pages (XSS in every interpolated value).

**Parser differentials (raw sockets required):** duplicate headers (security-
relevant ones: Authorization, Origin, Content-Length); comma-coalesced vs
array-valued headers; duplicate query params; duplicate form params
(first-wins? last-wins? reject? — per adapter); absolute-form request-target;
charset-suffixed content types; JSON body on form endpoints.

**SSRF/egress:** for every fetch the server can be made to perform — IP
encodings (decimal/hex/octal/short-form), IPv6 forms (v4-mapped, NAT64, ULA,
link-local), userinfo pivots, trailing dots, case tricks, punycode, redirects
to internal targets, DNS rebinding (validate-then-connect pinning?), cloud
metadata IPs, dangerous ports.

**AuthZ semantics:** scope/ceiling widening (undefined vs [] vs subset);
cross-client credential replay; privilege claims in attacker-acceptable
tokens; step-up enforcement.

**LLM trust boundary (agent frameworks / RAG / tool-calling apps — stochastic,
k-of-N, NOT single-shot):** model output is untrusted input — does anything
schema-validate it before a tool runs, or is the route schema `z.unknown()` /
`z.any()` passing raw args to `execute`? Are untrusted tool *descriptions*
(retrieved from a remote MCP server) injected raw into the model context — can
they override system instructions or trigger a consequential tool call with no
approval boundary? Is retrieved/RAG content or persisted agent memory framed as
untrusted, or does it re-enter the prompt with provenance authority? Does a
tool-output validator actually run, or always succeed? Because these attacks are
non-deterministic, run them as **k-of-N** evals (pin temperature/seed, N≥20,
report pass rate), never a single green run; judge with a model from a *different*
family than the one under test. Deterministic sub-parts (route accepts
`z.unknown()`; output validator is a no-op) CAN be executed single-shot and
should be — they're the load-bearing facts; the k-of-N pass rate is the
behavioral amplification on top. If no model key is available, execute the
deterministic parts and mark the stochastic amplification **blocked-by-harness**
with the eval design written out.

**Supply chain / fetched executable content:** anywhere the product fetches and
then *executes, imports, or installs* remote content — a plugin/tool/MCP-server
registry, a template/codegen fetch, a dynamic `import()` of a fetched URL, a
package suggested by the model. Is there subresource-integrity / a pinned hash /
an allowlist before the content runs, or is it fetched-then-executed? A
registry that pulls 30 third-party server configs with no pinning is a
supply-chain-RCE class distinct from egress SSRF (which only covers *targeting*).

## Rules

- **Re-execute before you report.** Every Phase-1 lead rated ≥7 is re-run as a
  PoC here *before* it enters the report. Static review inflates severity on
  framework defaults and provider-defended paths — expect ≥1 high-rated lead to
  fall (e.g. a "traversal" that the default provider already clamps; an SSRF
  form the URL normalizer defeats). Leads that don't reproduce go to a
  "downgraded-by-execution" lane with the specific reason, not to Confirmed.
- **Phase 2 owns single-request attacks against the PRIMARY path only.**
  Any hit — or any surprisingly lenient acceptance — becomes a Phase 4
  hypothesis: sweep it across every sibling (adapters/stores/modes) under
  sequence conditions. Phase 2 finds the crack; Phase 4 finds whether the
  crack is systemic. Neither re-runs the other's work.
- State each hypothesis + expected result BEFORE running; report observed.
- N=16 for races is a good default; count winners AND distinct artifacts.
- After every race, check the post-state: is the family/session wedged, dead,
  or alive? The aftermath is part of the invariant.
- A rejection proves nothing if your PoC sent garbage — when an attack
  "passes", verify the PoC actually sent the real attack (echo the payload).
- A rejection also proves nothing if the instrument never fired: assert
  interception counts and positive-control responses before "held" enters
  the output (harness-playbook checklist, item 4).

## Output

Per attack: name, hypothesis, expected, observed, PoC filename. Findings roll
up to the report's Confirmed/Held sections.
