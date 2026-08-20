# Phase 5: close the loop (always an ask: PR or disclosure)

Never auto-run this phase. After reporting, ask the user which disposition
fits:

- **On a repo the user owns, offer a PR.** Regression tests plus the
  invariant registry, on a branch and PR. Never straight to a default
  branch.
- **On third-party code, offer a private disclosure, but suggest it only
  when a finding is plainly bad or dangerous.** That means executed and
  high-consequence: credential theft, RCE-class bugs, tenant data
  exposure. For ordinary hardening findings, the report is the deliverable
  and the disclosure decision stays entirely with the user. Nudging
  disclosure for low-severity residue is noise. Do NOT commit tests to
  someone else's tree uninvited.

Close every assessment by asking what to do with the preserved artifacts:
keep the workspace, promote to regression tests, attach to the disclosure,
or discard. The user decides. The skill never disposes unilaterally.

## 0. Confirm the preserved workspace

Phase 5 consumes the close-out copy: every PoC plus `phase1-leads.md` and
`phase3-invariants.md`. Confirm it exists and that each PoC still runs
against the current tree. Anything that no longer reproduces is tagged
`as-recorded` with the original output attached, never silently trusted.

## 1. Promote PoCs to regression tests

A confirmed PoC must not die in `/tmp`. For each Confirmed or valuable
Held result:

- Reduce it to the smallest reliable test. Drop the harness scaffolding.
  Keep the invariant-violating sequence.
- Put it in the repo's existing test suite, named after the invariant, not
  the bug: "refresh-rotation-single-successor", not "fix-CVE-xyz".
- **Mutation-verify it.** Revert the fix, or break the invariant on
  purpose, and confirm exactly this test goes red. A regression test that
  passes against broken code is worse than none.

## 2. Living invariant registry

If the repo has a threat model or security doc, propose a
`SECURITY_INVARIANTS.md` or a section in the existing doc, next to it:

```markdown
| ID | Invariant | Established at | Assumed by | Regression test(s) | Last falsified |
|----|-----------|----------------|------------|--------------------|----------------|
| I4 | Auth code consumed exactly once | store consume* | token exchange | token-race.test.ts | 2026-08 (16-way, 3 stores) |
```

Keep each invariant precise and checkable ("consumed exactly once across
every replica and store"), never generic ("tokens are secure").

## 3. Re-falsify on architectural change

The registry turns future review into a targeted question:

> "Which invariants did this change touch, which new assumptions did it
> introduce, and what states have we not yet tried to construct?"

Triggers for re-running Phase 3 and Phase 4: a new store backend, a new
adapter, a new auth mode, a new cache, a new proxy hop, a new retry
mechanism, a new deployment topology, a new credential type, or a
config-format migration.

## 4. Resist checklist bloat

Do NOT let the methodology accrete a permanent mega-checklist of named
attacks. The Phase 2 taxonomy is disposable scaffolding. Its job is to
clear the known space. The durable artifacts are the **invariant
registry** and its **regression tests**. If a new attack class matters,
it enters through an invariant, not through a longer checklist.
