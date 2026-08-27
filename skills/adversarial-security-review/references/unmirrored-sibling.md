# Unmirrored-sibling patterns: the integration-seam defect class

The highest-yield class in integration-heavy targets (SDKs with adapters,
frameworks with ports, products with per-tenant or per-provider
implementations). Forged on the 2026-08 vercel/ai engagement: five confirmed
core findings and nine verified harness cells — every one this class, zero
primitive bugs. Run this sweep whenever the target has more than one
implementation of anything security-relevant.

## Definition

A cross-cutting security concern — approval, validation, redirect safety,
credential scoping, own-property lookups, cancellation, size caps — is
implemented correctly at ONE integration point and absent at its sibling.
The defect is never in the primitive; it is in the wiring. The proof shape
is constant: **the enforcing sibling's file:line is the exhibit**, and the
fix sketch is "mirror it."

## The generator function (why it happens, and where to predict it)

Concerns get implemented ONCE, at the first integration point, correctly.
Every sibling that arrives later inherits nothing — a concern is not a type
or a default, it is a call site — and nothing diffs the siblings. Four
corollaries, all field-observed:

1. **Age asymmetry.** The guard lives in the oldest sibling; the newest is
   unguarded. (streamText filtered its model-visible tool set; generateText,
   added later, parsed against the unfiltered set.)
2. **Third-class heuristic.** Input/tool classes added AFTER the guard was
   written are unguarded. (Cline: builtins gated and filtered; MCP tools,
   added later, auto-approved and provider-executed — "the unmirrored third
   tool class.")
3. **Trusted-state reuse skips revalidation.** Persisted credential env,
   resume blobs, and session state get overlaid onto live execution without
   the checks that fresh input receives.
4. **Docs assert the concern while code enforces it once or never** — the
   promise and the omission can ship in the same commit. Quote the promise;
   it is the report's counterweight to "experimental/working-as-designed"
   closures.

## Detection procedure: the concern × surface matrix

1. **Enumerate concerns mechanically.** Grep the contract/spec layer for its
   own names: dedicated files (a `permission-mode.ts` or
   `credential-forwarding.ts` is the vendor naming the concern for you),
   docs "never/always/must" sentences, and defensive helpers (`getOwn`-style
   wrappers = someone fought this battle in one place). A concern with its
   own contract file is prime.
2. **Enumerate surfaces mechanically** — registry/factory grep, never
   memory: every adapter, port, transport, provider, tool class. Include
   the shared composer (the code that passes the concern down); its omission
   is universal, not per-adapter.
3. **Fill the matrix**: per cell `enforced / missed / partial / absent-na /
   unknown`, each with file:line and a quoted line. A cell without a
   quotable line is `unknown`, never guessed. Expect inflation — a verify
   pass is mandatory (~25% overclaim rate observed on a 10-adapter sweep).
4. **Every missed/partial cell with an enforced sibling is a lead.** The
   sibling citation doubles as the unmirrored-fix exhibit.
5. **Execute the behavioral diff**: the same crafted input through each
   sibling's guard. One PoC, two adapters diverging, is the strongest filing
   shape — the divergence is the defense's existence proof.

## Report framing

- Feeds `finding-triage`: input origin is usually externally originated (the
  unguarded class carries untrusted input) against a defended boundary (the
  sibling) — the matrix's strongest cell. Run the capability-equivalence
  check anyway: name what the baseline attacker reaches through the
  SANCTIONED path across the same surface (the mediation delta), or expect
  an Informative closure.
- Experimental-prefixed surfaces take a severity reduction; the documented
  enforcement promise is the counterweight. Quote it verbatim, linked.

## Remediation to propose (bonus-eligible)

A **sibling-diff gate**: for each contract concern, a conformance test that
enumerates implementations and fails when one enforces and another doesn't —
per concern, not per adapter. Plus a PR-template question for new
tool/input classes: "which existing concerns apply to this class, and where
is each enforced?"

## Worked example (vercel/ai @ f8e24b6a1d, 2026-08)

| Concern | Enforced at | Missed at |
|---|---|---|
| Tool visibility | `stream-text.ts:2089` | `generate-text.ts:1073` |
| Approval | `generate-text.ts:1173` | `tool-caller-configuration.ts:129` |
| Schema validation | zod/valibot adapters | `jsonSchema()` → `validate-types.ts:67` |
| Redirect safety | transports `redirect:'error'` | `oauth.ts` discovery fetches |
| Own-property lookups | `getOwn` (dispatch) | `mcp-client.ts:1276` toolset build |
| permissionMode | `fx-harness.ts:611-615` | acp/cursor/claude-code native tools |
| MCP tool gating | `run-prompt.ts:882` (host tools) | `cline-session.ts:481-486` (MCP) |
| Credential env reuse | none — universal gap | 4 adapters' resumeData overlay |
| Cancellation | `pi-session.ts:857-864` | `cline-session.ts:518-521`, acp requestToolResult |
