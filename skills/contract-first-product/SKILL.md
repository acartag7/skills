---
name: contract-first-product
description: Design a new product by writing the rules first, in plain language — who can show up and in what state, tests that actually build the named case, then a paste-ready prompt for someone else to code. Use when starting a product, writing contract or onboarding docs, freezing v0, writing acceptance tests, walking review findings, or when holes keep escaping into PRs.
---

# Contract-first product

If a rule cannot be turned into a test that a **wrong** build
would fail, it is not frozen.

This chat writes the rules and the tests. It does not write the
product. It hands back a prompt.

**How you speak:** [talk.md](references/talk.md). No shop talk
to the human. One finding, then stop.

**How you ask them to decide:** [disclosure.md](references/disclosure.md).

**Holes that survive a freeze:** [holes.md](references/holes.md).

**Prompts for the other tab:** [prompts.md](references/prompts.md).

## What we keep doing wrong (do not drop these)

- Starting code in the design tab after they already said
  "tests here, build elsewhere."
- Asking "do it in this session?" when the plan exists.
- Deciding a number or a leftover risk and signing their name.
- Explaining with file paths and tool names instead of people
  and rooms.
- Fixing one of four actions and calling the hole closed.
- Using the same fake person in every test.
- Letting one rule hide another (writer vs room — talk.md A).

## Quality bar

If something is unclear, it fails. No silent pick, no "best
effort." Each rule lives in one place; other pages point at it.
A "never" with no real test is not shipped. Do not copy another
product's code or name. Do not lock a call they did not make.

## Steps (do not skip to tools)

```
- [ ] 1. Say the product in ordinary words
- [ ] 2. Who can show up, and in what state
- [ ] 3. One or two decisions at a time
- [ ] 4. Write the rule pages
- [ ] 5. For each never: can we write a test a wrong build fails?
- [ ] 6. Write those tests (they must build the named case)
- [ ] 7. They say freeze
- [ ] 8. Hand a prompt; do not start the builder here
```

### 1. Ordinary words first

What a person does. Who is hurt if it fails. What this version
is not. Protocol names come after that is sayable. Ask pickers
in the language they will use with their family.

### 2. Who can show up, and in what state

Before you name tools, fill the grid. Every cell is a named
result or "does not apply, because …". If a cell is empty, ask.
Do not invent.

**People (at least):** not signed in; signed in, no rooms; one
room; several rooms, none named; several, named right; named a
room they are not in; a machine login; first visit, no email or
name saved yet.

**Notes (at least):** missing; live; expired; taken back; real
id from the other room; empty write; too-big write.

### 3. Decisions

One or two. Disclosure + a picker. Separate "nobody chose this,
it's just wrong" from "this reverses a written choice." Never
write that they accept a leftover risk unless they said so.

### 4. Rule pages

Each file has one job:

| File | Job |
| --- | --- |
| quality-bar | how we work; when a rule may change |
| contract | tools, errors, boot, the nevers |
| onboarding | how a human joins |
| http-api | website routes (if there is a site) |
| threat-model | what a signed-in attacker can try |
| open-questions | only what is still open |
| future | not this version |

If the route list has PATCH, the "other website" sentence cannot
say only POST and DELETE.

If the contract stores "which client wrote this," the test
login must be able to say website vs their agent.

"Who am I" with no profile yet must not demand a profile.

App packages are named for the **app**. Leave the short name
for a later library.

If a sibling library is about to release, write the ask. Do
not grow a second copy here.

### 5. Can a wrong build fail?

For every never: who, what they do, the exact refusal. If you
cannot say it, the never is not ready.

### 6. Tests that build the case

A test only counts if a wrong build fails it. Use
[holes.md](references/holes.md). In short:

1. The test does what its title says.
2. Other-room tests use a **real** id from that room, on
   **every** action that takes an id. Writer-only actions:
   the same person must have written that note (talk.md A).
   Two rooms and no room named: every action that needs a
   room, not only the list (talk.md E).
3. Check the specific refusal. "Any error except not-built"
   is not enough (talk.md B).
4. Do not catch the test's own failure.
5. New person every time. Quotas are real.
6. Garbage in is a refusal, not a save.

When locked tests change, update the lock file in the same
change.

### 7. Freeze

They say the word. Then tests, then code. Slices are fine:
lock this slice's tests before that slice's code. A never
with no test is not shipped.

### 8. A prompt, not this tab

Write the prompt. They paste it. Template:
[prompts.md](references/prompts.md).

Commits: `type: short why`. No bot co-authors. No "Made with"
lines. Do not force-push `main`.

## This skill is not

Not a launch. Not a break-the-product assessment (that is
`adversarial-security-review`). Not permission to write the
product. Not permission to close opens for them.

## What runs this well

The failure mode is eagerness: implement in this tab, dump
ten holes, or lock a leftover so the chat looks finished.
The model that runs this well is blunt, asks one thing, and
stops. Evals live in `evals/` (writer hides room; any-throw
is not the guard; design tab does not code; talk plain;
omit room on every tool).
This is **0.1.0**. 1.0.0 waits on a second product and a
real eval run.
