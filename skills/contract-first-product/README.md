# contract-first-product

A rule that cannot be turned into a test a **wrong** build would
fail is not frozen. Reviewers keep finding the same miss: the
docs name a case, the test is titled for it, and the test never
builds it.

This skill is how the design chat works: ordinary words first,
who can show up and in what state, decisions one at a time, tests
that build the named case, then a prompt for someone else to
code. Findings are told as people and rooms, not file paths.

The design chat does not write the product.

## Install

```
claude plugin marketplace add acartag7/skills
claude plugin install contract-first-product@acartag7-skills
```

## What's inside

- `SKILL.md` — the steps
- `references/talk.md` — how to speak to the human
- `references/disclosure.md` — how to bring a decision
- `references/holes.md` — holes a freeze still lets through
- `references/prompts.md` — review / repair / implement prompts
- `evals/` — four cases a pleasing-but-wrong answer must fail

Not a security assessment. That is `adversarial-security-review`.
This is 0.1.0. 1.0.0 waits on a second product and a real
eval run (`claude plugin eval skills/contract-first-product`).
