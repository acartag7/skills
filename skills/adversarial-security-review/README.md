# adversarial-security-review

A code read produces opinions. A scanner dump produces noise. Neither
proves the claim that matters: *"an attacker can't do X here."* This skill
proves or kills those claims — every finding is a PoC that ran, with the
hypothesis stated before execution and expected-vs-observed recorded.

Every attack that fails goes in **Held** with the exact interleaving ruled
out — the negative results are the backbone of the report, not a footnote.
And when your own harness lies (a stub that never fired, a DNS server that
dropped every answer), the skill is built to catch itself before it
reports a false "held."

**Three depths**: static claims-vs-code review (no execution) → executed
attack-taxonomy PoCs → invariant-first falsification under concurrency and
partial failure, where the bugs have no CVE name yet.

## Install

```
claude plugin marketplace add acartag7/skills
claude plugin install adversarial-security-review@acartag7-skills
```

## What's inside

- `SKILL.md` — the non-negotiable rules and phase map
- `references/phase-1..5` — static review, executed taxonomy, invariant
  extraction, seam falsification, close-the-loop
- `references/harness-playbook.md` — harness patterns, honesty checklist,
  and the harness-lie gallery (worked self-inflicted false positives)
- `references/report-template.md` — report shape, plain-language layer,
  disclosure-note pattern
- `evals/` — judgment cases with adversarial graders

Versioned independently in this repo (tagged
`adversarial-security-review--v<version>`); see the root
[CHANGELOG](../../CHANGELOG.md).
