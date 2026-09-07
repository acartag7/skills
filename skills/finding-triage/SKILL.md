---
name: finding-triage
description: Classify an executed security finding and decide its venue — bounty submission, repo issue, hardening note, or drop — using the input-origin x boundary matrix and the closure-pattern catalog. Use after a PoC lands and BEFORE any drafting; nothing reaches a bounty form without a triage verdict.
---

# Finding Triage

Classify every executed security finding BEFORE drafting anything, and
decide its venue: bounty submission, repo issue, hardening note, or drop.
One matrix, two axes, a closure-pattern catalog — run per finding, output
a verdict table. Sits between the assessment (adversarial-security-review)
and the drafting (bounty-report-craft): **nothing reaches a bounty form
without a triage verdict that says it belongs there.**

Forged on a 2026 engagement that spent two clean filings to learn this:
both reports had executed mechanics, undisputed behavior, airtight dedup —
and both closed Informative on threat-model grounds that were visible in
the matrix before a word was drafted. The second one was predicted by the
researcher at filing time; the matrix exists so the first one would have
been too.

## The two axes

**Axis 1 — input origin.** Trace the malicious input to who must control
it for the attack to run:

- **Developer configuration** — constructor options, env vars,
  operator-written config files. The party who sets these IS the
  application, from the target's viewpoint.
- **Externally originated** — server responses, model output, user data
  the target parses, files the target reads at documented paths. This is
  the target's parsing/validation responsibility.
- **Ambient environment** — PATH, umask, host state outside config.
  Local-attacker precondition; grade accordingly.
- **Hybrid** — config that carries externally-originated content (a
  tenant-supplied string stored then passed to a constructor). Classify by
  WHERE the trust break happens: the target sees config; the application
  saw untrusted input. Hybrid findings are the contested middle — the
  matrix output is "repo issue unless the program has closed similar
  config-input findings as valid."

**Axis 2 — boundary defense, by evidence.** Does the target visibly
defend against misuse of this input? Evidence is greppable, cite lines:

- validated sibling field / scrubbed sibling path (also your
  unmirrored-fix exhibit)
- threat-model comments, adversarial tests, security-release fixes
  touching the area
- nothing → undefended

## The matrix → venue

| | Boundary defended | Boundary undefended |
|---|---|---|
| **Config origin** | Borderline: file only if a stark unmirrored fix | **Repo issue.** Filing predicts Informative |
| **External origin** | **File — strongest shape** | File if impact is real; expect working-as-designed |
| **Ambient env** | Repo issue (defense exists elsewhere) | Repo issue; local precondition caps severity |

Then overlay the program's exclusions (see
adversarial-security-review's program-intelligence reference for pulling
policy): DoS-class impact, beta windows, scope-row eligibility each
independently kill or delay a filing regardless of the matrix.

## The capability-equivalence check (run on every candidate)

Enumerate what the baseline attacker — the one your PRECONDITION already
describes — can do without the bug. Then ask: is the finding's impact a
**new capability** that attacker lacked, or an **efficiency delta**
(fewer calls, faster, one spelling instead of N)? Efficiency deltas close
as Informative ("the precondition already confers the claimed impact") no
matter how clean the mechanics. Run this on capability SETS, not call
counts.

## Closure-pattern catalog (named, with draft-time signals)

- **Capability-equivalence** — your delta is efficiency, not capability.
  Venue: repo issue.
- **Config-trust-class** — your attacker needed constructor/env/config
  power. The argument that feels strongest and loses anyway: "this knob's
  name hides that it selects the credential's destination." Silent
  authority promotion is real engineering criticism and a losing bounty
  argument. Venue: repo issue.
- **DoS-exclusion** — availability-only impact against a program that
  excludes DoS. Venue: repo issue.
- **Working-as-designed** — externally-originated input, undefended
  boundary, no advisory history. File only with impact that speaks for
  itself.
- **Duplicate / one-underlying-issue** — same guard class or same
  underlying cause as a credited or filed item. Merge or clearly distinct
  guards+impact, with the diff exhibit.
- **Precondition-too-strong** — the attack needs a foothold that already
  implies the impact (chained access, local compromise). State the
  precondition, claim one tier net, or drop.

## Output

Per finding, one row (plus a confidence and the revisable-only-on-new-
evidence rule):

| finding | input origin | boundary evidence (file:line) | baseline-capability delta | exclusion hits | venue | predicted closure if filed |
|---|---|---|---|---|---|---|

Log the prediction. When the closure lands, record whether it matched —
the catalog's signals calibrate against your own program history, and a
misprediction is either new doctrine to name or a classification error to
fix. Triage verdicts change on new evidence, never on enthusiasm.
