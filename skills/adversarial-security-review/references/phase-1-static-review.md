# Phase 1 — Static claims-vs-enforcement review

Goal: build the attacker's mental model and kill cheap hypotheses. Read-only.

## Steps

0. **Enumerate the project's own scanners first.** Read the CI config
   (CodeQL/Bandit/Semgrep/Dependabot/pip-audit or the equivalent). Assume they
   already cleared the lexical classes — bare `eval`, `pickle`, hardcoded
   secrets — so don't spend Phase 1 re-finding them; bias toward what scanners
   can't see: logic bugs, guard-coverage seams, invariant violations,
   unenforced claims. Also note what CI *gates* on vs merely reports.

1. **Map the trust boundaries.** Entry points (HTTP routes, CLI, file loads),
   parsers, crypto/verification, storage, network egress, identity sources.
   List every place untrusted input crosses into trusted state.

2. **Harvest the guarantee sentences.** Grep docs/README/comments for
   `never|always|cannot|enforced|rejected|only|must|guarantee|safely`.
   For EACH hit: name the `file:line` of the enforcing code or test. A claim
   naming a function must name the function that actually does the work
   (verifying wrapper vs pure validator). Unenforced claims = lead list.
   Explicitly enumerate *documented deliberate exceptions* — comments or
   config saying "fail-open: …", "intentionally permissive", "we allow X
   for Y" — each is either a claim the code disagrees with or a posture
   someone consciously chose; both are highest-value leads.

3. **Guard ordering.** For every rejection path: does the guard run BEFORE
   store writes and success-audit emits? A success audit followed by a failure
   for the same operation means the guard is in the wrong place.

4. **Fail-open hunt.** Grep for `catch {` / empty catch / `?? default` on
   security paths. Each one: does an exception, undefined, or malformed input
   become ALLOW? Documented fail-open (e.g. rate limiters) is a finding to
   report, not assume acceptable.

5. **Defaults audit (first-class for frameworks/libraries).** What happens with
   zero configuration? No-auth modes, dev flags, noop limiters, ephemeral keys,
   wildcard binds, client-supplied identity/tenant fields. The default IS the
   security posture for most users — for a *framework* it is the product
   security, not a footnote. Treat the default deployment shape as its own
   surface: stand up the zero-config path in your head and ask "who can reach
   what, with no operator config?" Each dangerous default is a lead, **tagged
   `defect-type: insecure-default`** (vs `code-bug` or `operator-misconfig`) —
   the tag drives the fix path (fail-closed-by-default code change vs
   docs+guardrails vs runbook) and calibrates severity honestly. A documented
   default is still a finding; "it's in the docs" does not make an open-by-default
   posture safe.

6. **Dependency surface & packaging reality.** Runtime deps, peer deps, native
   modules. Minimum versions and known-vuln ranges. Then check the declared
   minimum runtime (`requires-python` / `engines`) against what the code
   actually uses: grep for stdlib APIs newer than it (`datetime.UTC`,
   `tomllib`, `typing.Self`, `TaskGroup` on the Python side; equivalents
   elsewhere) and confirm the primary entrypoints import on the declared
   minimum. "Installs clean but ImportError-s on import" is a ship-vs-claim
   gap — reliability, not security — but it caps any "safe to use on X–Y"
   verdict the report issues.

7. **Sibling enumeration (mechanical, not eyeball).** For every interface with
   multiple implementations — store backends, framework adapters, auth
   providers, route families, fs providers — *enumerate them all* (grep the
   registry/factory, not your memory) and assert each is swept by the checks
   above. A guard applied to one sibling and not another (e.g. path-traversal
   check on skill routes but not fs routes; containment on one fs provider but
   not another) is the canonical "defense exists but isn't wired in" gap. The
   rule "a bug in one is a hypothesis about all" is only as strong as the
   enumeration that feeds it — make the sibling list an artifact, not a guess.
   For a guard-coverage gap, the Phase-2 evidence is a **behavioral diff**:
   the same crafted input through each sibling's guard (one rejects it, one
   wraps it raw) — not merely a grep showing one calls the guard and the
   other doesn't.

8. **Test-suite hygiene sweep.** Run `pytest --collect-only` (or the
   equivalent) and classify every collection error: all missing-optional-deps,
   or real failures hiding in the noise? Flag test files that cannot be valid
   modules (e.g. `test_foo().py` — parentheses in the filename is a classic
   agent-generated artifact that no reviewer read). Not security — but a
   process-quality signal that contextualizes the posture: the same weak
   review pipeline that ships cruft ships unenforced guards.

## Output

Write these to the PoC workspace as FILES, not just the reply — later phases
and later sessions reference them mechanically; if the file doesn't exist,
the phase isn't done.

- `phase1-leads.md` — the lead list, one row per lead: id, risk 1–10,
  `defect-type` tag (code-bug | insecure-default | operator-misconfig),
  `file:line`, one-line attack hypothesis. Phase 2 re-executes every lead
  rated ≥7 by reading this file. Row format (a markdown table, so fan-out
  delegates can be validated mechanically):

  | id | risk | defect-type | file:line | hypothesis |
  |----|------|-------------|-----------|------------|
  | L1 | 8 | insecure-default | guard.py:42 | zero-config bind skips the guard |
- The trust-boundary map (bullets, not prose).
- The sibling-implementation artifact: the enumerated list of every store /
  adapter / provider / route-family, with a check mark per sibling.
- The scanner/gate inventory (step 0), the min-runtime import verdict
  (step 6), and the collection-error classification (step 8) — these cap the
  final usage recommendation.
- First draft of the invariant list (refined in Phase 3): what does this code
  *depend on but never re-check*?

Do NOT report findings yet — Phase 1 produces hypotheses only. **Static leads
overclaim** — especially on framework defaults and provider-defended paths.
Every lead rated ≥7 must be re-executed in Phase 2 before it enters the report;
leads that don't reproduce on execution go to a "downgraded-by-execution" lane,
not Confirmed.
