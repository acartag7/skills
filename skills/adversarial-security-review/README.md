# adversarial-security-review

A code read produces opinions. A scanner dump produces noise. Neither
proves the claim that matters: *"an attacker can't do X here."* This
skill proves or kills those claims. Every finding is a PoC that ran,
with the hypothesis stated before execution and expected against
observed recorded.

Every attack that fails goes in **Held** with the exact interleaving
ruled out. The negative results are the backbone of the report, not a
footnote. And when your own harness lies, whether through a stub that
never fired or a DNS server that dropped every answer, the skill is
built to catch itself before it reports a false "held."

**Three depths.** Static claims-vs-code review, with no execution. Then
executed attack-taxonomy PoCs. Then invariant-first falsification under
concurrency and partial failure, where the bugs have no CVE name yet.

## Can this actually find new bugs? (plain English)

An AI model learns from a huge pile of everything ever written. That
pile is its "corpus", so its first suggestions are always bugs it has
read about before. Bugs come in three kinds:

1. **Known bug types in unchecked places**: a lock that exists but was
   only installed on 2 of the 5 doors. Scanners can't find these,
   because they match words, not missing locks. This skill finds them
   reliably.
2. **Bugs unique to this system**, born from how your specific program
   assembles its parts. The skill finds these sometimes. They are its
   best trick.
3. **A genuinely new type of bug**, never published anywhere.

For the third kind, the skill doesn't ask the model to be creative,
because creativity falls back to old knowledge. It asks the model to run
experiments and notice surprises: read the actual library code your
program uses, send the same tricky input to two parts of the system and
check whether they disagree, poke the program until something weird
happens, then investigate the weirdness. The surprise comes from your
running program, not from the model's memory. **The world invents; the
model reduces.** The "no finding without a working proof" rule is the
filter that keeps rare-but-real separate from confident nonsense.

## Install

```
claude plugin marketplace add acartag7/skills
claude plugin install adversarial-security-review@acartag7-skills
```

## What's inside

- `SKILL.md`: the non-negotiable rules and the phase map.
- `references/phase-1..5`: static review, executed taxonomy, invariant
  extraction, seam falsification, close-the-loop.
- `references/harness-playbook.md`: harness patterns, the honesty
  checklist, and the harness-lie gallery of worked self-inflicted false
  positives.
- `references/report-template.md`: the report shape, the plain-language
  layer, and the disclosure-note pattern.
- `evals/`: judgment cases with adversarial graders.

Versioned independently in this repo, tagged
`adversarial-security-review--v<version>`. See the root
[CHANGELOG](../../CHANGELOG.md).
