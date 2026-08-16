# Freeze a case

The eval suite is the experiment. Each case is a **frozen exact
head**: the tree as it stood when a CLEAN local pass missed a
leftover sibling, reconstructed at mechanism level.

Do not copy a product name, a host, a personal path, or a real
commit SHA into this repo. The gold is the **class** and the
**empty cell**, not the incident.

## When to add a case

Add one when hosted review finds a leftover sibling after a
local CLEAN, **or** when a follow-up PR is only the next cell
of a class you just merged.

Do not add a case for a new contract edge (that is STOP, not a
review miss) or for a finding the skill already covers with an
equivalent head.

## How to freeze

1. **Name the behavior** in one sentence. Example: "store errors
   still reach the client on write paths."
2. **Name the empty cell** (M1–M7). Example: M1 `create` /
   `{ ok: false }` still unwrapped.
3. **Rebuild a tiny head** in the prompt: the few files that
   make the miss visible. Rename symbols. Keep the mechanism
   (wrap `find` only; leftover "always 200"; prefix unique
   accepted as unique).
4. **Frame it as a CLEAN pass.** The implementer believes the
   named instance is done. That is the experimental condition.
5. **Write the grader** against the class, not a wording:
   FAIL if the review PASSes the head or only restates the
   instance the story already fixed; PASS only if it names the
   empty cell and refuses CLEAN.
6. **Keep a control.** If you add many FAIL-the-head cases,
   keep `class-closed` (or add another) so a review that always
   invents a P1 cannot farm the suite.

## Case layout

```text
evals/<id>/prompt.md
evals/<id>/graders/<id>.md
```

`prompt.md` starts with: you are running this skill on this
exact head. Include the fake SHA, the prior CLEAN, and the
files. Do not ask the model to fetch a real repository.

`graders/<id>.md` ends with exactly `VERDICT: PASS` or
`VERDICT: FAIL`.

## What "good" is

A good review **refuses** the CLEAN head and names the empty
cell. A pleasing review agrees the named instance is fixed and
PASSes. The grader exists to fail that pleasing answer.

On `class-closed`, good is PASS with filled cells and no
invented blocker.
