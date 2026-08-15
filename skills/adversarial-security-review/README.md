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

## Can this actually find new bugs? (plain English)

An AI model learns from a huge pile of everything ever written — that pile
is its "corpus." So its first suggestions are always bugs it has read
about before. Bugs come in three kinds:

1. **Known bug types in unchecked places** — a lock that exists but was
   only installed on 2 of the 5 doors. Scanners can't find these (they
   match words, not missing locks). This skill finds them reliably.
2. **Bugs unique to this system** — born from how your specific program
   assembles its parts. The skill finds these sometimes; they are its
   best trick.
3. **A genuinely new type of bug** — never published anywhere.

For #3, the skill doesn't ask the model to be creative (that falls back
to old knowledge). It asks it to run experiments and notice surprises:
read the actual library code your program uses, send the same tricky
input to two parts of the system and check whether they disagree, poke
the program until something weird happens — then investigate the
weirdness. The surprise comes from your running program, not from the
model's memory: **the world invents; the model reduces.** And the "no
finding without a working proof" rule is the filter that keeps
rare-but-real separate from confident nonsense.

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
