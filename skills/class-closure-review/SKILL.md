---
name: class-closure-review
description: Exact-head local PR review that refuses PASS until a defective behavior is closed across every sibling cell, not just the named instance. Use when reviewing a pull request locally, after a hosted review finding, before requesting another review round, or when leftover claims, unswept adapters, stored-state, or composition-root misses keep surviving a CLEAN pass.
---

# Class-closure review

Exact-head local review. The unit of work is a **behavior**, not a
`file:line`. PASS requires a filled matrix. An empty applicable cell
is FAIL, even if the named instance looks fixed.

A prior CLEAN is not evidence. "Swept siblings" with no cells is FAIL.

## When this is the review

- Local review of a PR or exact commit before hosted review.
- After a hosted finding, **before** the next push or re-request.
- Leftover claims, one-adapter fixes, stored-vs-entry misses, or
  follow-up PRs that are the next sibling of the last merge.

Do not edit the tree. Do not implement the fix in this pass.

## Rules

1. Review the exact named commit. Print the full SHA. Any later
   push makes the result stale.
2. Name the defective **behavior** in one sentence (what still
   works that the change says cannot). Then fill cells. Do not
   start from the comment that happens to be open.
3. Load [matrices.md](references/matrices.md). Mark every cell
   `hit` / `n/a` / `clean`. Inventing a reason to skip a row that
   exists in the tree is FAIL.
4. A guarantee verb (`never`, `always`, `cannot`, `enforced`,
   `rejected`, `only`, `must`) needs enforcing code **and** a
   test that goes red if that code is removed. Softening the
   sentence is not the fix.
5. After any finding — local or hosted — do not push until the
   **class** is closed. Fixing the named instance and
   re-requesting review is the round multiplier this skill exists
   to stop.
6. If review discovers an edge the contract never named, **STOP**.
   That is a contract hole, not another review round. See
   [output-contract.md](references/output-contract.md).

## Steps

```
- [ ] 1. Pin REVIEWED_SHA (full) and the base
- [ ] 2. Read the whole diff, not the ticket summary
- [ ] 3. Fill M1–M7; skip a matrix only when the change cannot touch it
- [ ] 4. Grep leftover claims across every page that still describes the old rule
- [ ] 5. For each new guard: would reverting the shipped function (not a helper) go red?
- [ ] 6. Emit the output contract — no PASS with an empty applicable cell
```

If this head answers a prior finding, the **CLASS CLOSURE** block
must list the other cells of that behavior and show they were
checked. "Fixed the reported line" is not closure.

## Output

Follow [output-contract.md](references/output-contract.md) exactly.

## Adding a frozen case

When a hosted round finds a leftover sibling after a CLEAN local
pass, freeze it: [freeze-a-case.md](references/freeze-a-case.md).
Do not put product names, commit SHAs, or hostnames in this repo.
