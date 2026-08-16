# Prompts (design chat writes these; human pastes them)

Do not start an implementer from the design tab. Fill the brackets.
Conventional commits. No AI co-author trailers. No "Made with" footers.

## Freeze-readiness review (Fable-shaped)

```
You are reviewing a FROZEN or about-to-freeze contract set, not a
draft feature list. Do not invent product requirements. Do not
reopen Held items unless the text newly contradicts them.

Read ALL contract-bearing docs in the repo (quality bar, contract,
onboarding, http-api if present, threat model, namespace, README).
Do not treat future.md / open-questions.md as v0.

For every never: can a hostile test be written? Walk
references/holes.md classes 1–10 against the tests AND the docs.

Output: Holes / Collisions / Pretend-decided / Suggestions / Held.
Rank within each section. Do not invent remaining opens. Do not
write code.
```

## Test repair (after review finds construction holes)

```
MODEL: gpt-5.6-sol
EFFORT: xhigh
CONTEXT: safe
FALLBACK: grok-4.5
WHY: Hostile acceptance-test repair.

Fix only the named construction holes. Do not invent product rules.
Do not implement the runtime beyond harness exports that must exist
for a fixture (those throw NotImplementedError on the test branch).

Rules: holes.md 1–6. Unique subjects. Real foreign ids on every
id-taking tool. Dedicated error names. Do not catch AssertionError.
Rewrite the freeze manifest in the same change.

Conventional commit, no AI trailers. Do not force-push main.
```

## Implementation against hashed tests

```
MODEL: grok-4.5
EFFORT: high
CONTEXT: safe
FALLBACK: gpt-5.6-terra
WHY: Narrow implementation against already-written tests.

Implement only what the hashed tests and the owning contract
section require. Do not special-case test strings. Do not grow a
second orchestrator. Session/call context must carry every field
the contract persists (holes.md 7–9).

pnpm test (or the repo's equivalent) green. Conventional commit,
no AI trailers. No website / admission code unless that batch's
tests are already hashed.
```

## After a second review pass

If the first repair fixed only one verb or only "not X", assume
classes 2 and 4 still apply. Do not declare the slice done until
a review pass finds nothing in holes.md 1–10.
