# ADR 0005 — class-closure-review from hosted review rounds

Status: accepted (2026-08-16)

## Context

A public authorization library's hosted review history (130
pull requests) showed extra rounds clustering on one shape: a
CLEAN local pass closed the named instance, then the next head
was the same defect on the next adapter, leftover sentence,
stored row, or generated starter. Generic "sweep siblings"
prose was already in the house rules. It still signed CLEAN.

The method lived only in that measurement. The next local
review would miss the same cells.

## Decision

Ship `class-closure-review` as its own plugin (0.1.0). The
imported rules are the ones that actually multiplied rounds.
No product name, commit SHA, or host in this repo.

| Gap | Landed in |
| --- | --- |
| Review closes `file:line`, not the behavior | SKILL rules 2 and 5 |
| "Swept siblings" with no cells | matrices.md; empty cell = FAIL |
| Leftover guarantee after a code fix | M3; leftover-claim eval |
| Wrap one call, claim every call | M1; one-call-site eval |
| Prepare-time policy, stored row skipped | M2; stored-not-rechecked eval |
| Guard exists, after the write | M4; guard-after-open eval |
| "Has unique name" is not shape | M6; name-not-shape eval |
| Library and example fixed, starter not | M1 composition root; starter-not-library eval |
| A review that always invents a blocker | class-closed control eval |
| Next hosted miss has nowhere to go | freeze-a-case.md |

1.0.0 waits until the method is used on real local review
passes and the eval suite has actually been run.

## Consequences

Local review before a hosted round has a refuse-to-PASS
contract. Hosted review is supposed to verify, not discover
the next sibling. A follow-up PR whose whole job is the next
cell of the last merge is recorded as a skill miss, then
frozen as a case.
