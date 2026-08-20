# Phase 1: static claims-vs-enforcement review

Goal: build the attacker's mental model and kill cheap hypotheses.
Read-only.

## Steps

0. **Enumerate the project's own scanners first.** Read the CI config
   (CodeQL, Bandit, Semgrep, Dependabot, pip-audit, or the equivalent).
   Assume they already cleared the lexical classes, so bare `eval`,
   `pickle`, and hardcoded secrets are not your targets. Bias toward what
   scanners can't see: logic bugs, guard-coverage gaps, invariant
   violations, unenforced claims. Also note what CI *gates* on and what it
   merely reports.

1. **Map the trust boundaries.** Entry points (HTTP routes, CLI, file
   loads), parsers, crypto and verification, storage, network egress,
   identity sources. List every place untrusted input crosses into trusted
   state.

2. **Harvest the guarantee sentences.** Grep docs, README, and comments for
   `never|always|cannot|enforced|rejected|only|must|guarantee|safely`. For
   each hit, name the `file:line` of the enforcing code or test. A claim
   naming a function must name the function that actually does the work:
   the verifying wrapper, not the pure validator. Unenforced claims become
   lead-list rows. Explicitly enumerate *documented deliberate exceptions*
   such as comments or config saying "fail-open here", "intentionally
   permissive", or "we allow X for Y". Each is either a claim the code
   disagrees with or a posture someone consciously chose. Both are
   highest-value leads.

3. **Guard ordering.** For every rejection path, check whether the guard
   runs BEFORE store writes and success-audit emits. A success audit
   followed by a failure for the same operation means the guard is in the
   wrong place.

4. **Fail-open hunt.** Grep for `catch {`, empty catch blocks, and
   `?? default` on security paths. For each one, ask: does an exception,
   undefined, or malformed input become ALLOW? Documented fail-open (for
   example in rate limiters) is a finding to report, not an acceptable
   assumption.

5. **Defaults audit, first-class for frameworks and libraries.** What
   happens with zero configuration? Look for no-auth modes, dev flags,
   no-op limiters, ephemeral keys, wildcard binds, and client-supplied
   identity or tenant fields. The default IS the security posture for most
   users. For a framework, the default is the product security, not a
   footnote. Treat the default deployment shape as its own attack surface:
   stand up the zero-config path in your head and ask who can reach what
   with no operator config. Tag each dangerous default
   `defect-type: insecure-default` (not `code-bug` or
   `operator-misconfig`). The tag drives the fix path: fail-closed code
   change, docs plus guardrails, or runbook. A documented default is still
   a finding. "It's in the docs" does not make an open-by-default posture
   safe.

6. **Dependency surface and packaging reality.** Runtime deps, peer deps,
   native modules, minimum versions, known-vuln ranges. Check the declared
   minimum runtime (`requires-python`, `engines`) against what the code
   actually uses: grep for stdlib APIs newer than the declared minimum
   (`datetime.UTC`, `tomllib`, `typing.Self`, `TaskGroup` on the Python
   side, and equivalents elsewhere) and confirm the primary entrypoints
   import on that minimum. "Installs clean but fails on import" is a
   ship-versus-claim gap: reliability, not security. It still caps any
   "safe to use on X" verdict the report issues.

7. **Version-delta lead harvesting, when the deployed version is
   pinned.** If the target runs a known version older than upstream
   HEAD, read the changelog between them and harvest every "Fixed"
   entry touching the target's components as a lead-list row: each is an
   admitted bug the deployed code still carries. Read the patch, not
   just the entry. The fix context names the exact faulty line and the
   triggering condition. Tag each row with whether a live primitive can
   reach the patched path. That cross-reference turns a changelog into a
   pre-ranked test queue. Upstream did the admitting. The assessment
   does the reaching.

8. **Sibling enumeration, mechanical and never by eye.** For every
   interface with multiple implementations, enumerate them all by grepping
   the registry or factory, not your memory. Store backends, framework
   adapters, auth providers, route families, filesystem providers. A guard
   applied to one sibling and not another, for example a path-traversal
   check on skill routes but not on filesystem routes, is the canonical
   "defense exists but isn't wired in" gap. The rule "a bug in one is a
   hypothesis about all" is only as strong as the enumeration that feeds
   it, so make the sibling list an artifact, not a guess. For a
   guard-coverage gap, the Phase-2 evidence is a **behavioral diff**: the
   same crafted input through each sibling's guard, where one rejects it
   and one wraps it raw. A grep showing one calls the guard and the other
   doesn't is not that evidence.

9. **Test-suite hygiene sweep.** Run `pytest --collect-only` or the
   equivalent. Classify every collection error: all missing optional deps,
   or real failures hiding in the noise? Flag test files that cannot be
   valid modules, for example `test_foo().py`, where parentheses in the
   filename are a classic agent-generated artifact no reviewer read. Not
   security, but a process-quality signal: the same weak review pipeline
   that ships cruft ships unenforced guards.

## Output

Write these to the PoC workspace as FILES, not just the reply. Later
phases and later sessions reference them mechanically. If the file doesn't
exist, the phase isn't done.

- `phase1-leads.md`: the lead list, one row per lead with id, risk 1 to
  10, `defect-type` tag (code-bug, insecure-default, or
  operator-misconfig), `file:line`, and a one-line attack hypothesis.
  Phase 2 re-executes every lead rated 7 or higher by reading this file.
  Row format is a markdown table, so fan-out delegates can be validated
  mechanically:

  | id | risk | defect-type | file:line | hypothesis |
  |----|------|-------------|-----------|------------|
  | L1 | 8 | insecure-default | guard.py:42 | zero-config bind skips the guard |

- The trust-boundary map, as bullets rather than prose.
- The sibling-implementation artifact: the enumerated list of every store,
  adapter, provider, and route family, with a check mark per sibling.
- The scanner and gate inventory (step 0), the minimum-runtime import
  verdict (step 6), and the collection-error classification (step 9).
  These cap the final usage recommendation.
- A first draft of the invariant list, refined in Phase 3: what does this
  code *depend on but never re-check*?

Do NOT report findings yet. Phase 1 produces hypotheses only. **Static
leads overclaim**, especially on framework defaults and provider-defended
paths. Every lead rated 7 or higher must be re-executed in Phase 2 before
it enters the report. Leads that don't reproduce on execution go to a
"downgraded-by-execution" lane, not Confirmed.
