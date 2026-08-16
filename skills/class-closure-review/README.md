# class-closure-review

A local review that says CLEAN on the named instance, then hosted
review finds the same defect on the next adapter, leftover
sentence, or stored row, is not a thoroughness problem. It is a
missing unit of work: the **class**, not the `file:line`.

This skill is the local exact-head pass that refuses PASS until
every applicable matrix cell is filled. Empty cell = fail. A
prior CLEAN is not evidence.

## The experiment

The evals are frozen exact-heads reconstructed from a hosted
review history: the tree as it stood when a CLEAN local pass
missed a leftover sibling. Six heads a pleasing CLEAN must
fail; one closed-class control so a review that always invents
a blocker cannot farm the suite.

Gold is the **empty cell**, not the product. How to add the
next hosted miss: [freeze-a-case.md](references/freeze-a-case.md).

```
claude plugin eval skills/class-closure-review
```

(`claude plugin eval` was CLI-gated early access as of 2026-08;
the cases ship regardless.)

## Install

```
claude plugin marketplace add acartag7/skills
claude plugin install class-closure-review@acartag7-skills
```

## What's inside

- `SKILL.md` — rules, steps, when to STOP
- `references/matrices.md` — M1–M7 (paths, stored state, leftover
  claims, guard order, wire form, schema shape, test bite)
- `references/output-contract.md` — required PASS/FAIL/STOP fields
- `references/freeze-a-case.md` — how a hosted miss becomes a case
- `evals/` — seven judgment cases

This is 0.1.0. 1.0.0 waits until the method is used on real
local review passes and the eval suite has actually been run.
