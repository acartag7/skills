# ADR 0001 — Skill hardening from two field assessments

Status: accepted (2026-08-14)

## Context

`adversarial-security-review` ran two full Level-3 assessments — a Python
knowledge-graph framework and a TypeScript agent framework (targets
unnamed; assessment records live in a private vault). Each produced a gap
list of methodology rules that only surfaced under field conditions; gaps
hit by BOTH assessments were treated as priorities. Until now the skill
lived unversioned in `~/.claude/skills/`, and edits landed from different
sessions with no history — the final state had to be reconstructed by
diffing file timestamps and a stale consolidated draft against the live
files.

## Decision

The skill moves into this repo (marketplace `acartag7-skills`, one plugin per
skill); all future edits happen here with a diff trail. The imported state is
the union of the live files and the consolidated draft — the live files were
newer for `harness-playbook` (Python target track, harness-lie gallery) and
`report-template` (verification attribution), the draft was newer for
`SKILL.md` (behavioral-diff rule 5, workflow/orchestration boundary).

Gap → where it landed:

| Gap (both assessments) | Landed in |
|---|---|
| Static-confirmed ≠ behaviorally true; ≥7 leads re-executed; downgraded-by-execution lane | phase-1 Output; phase-2 Rules |
| Defect-type lane (code-bug / insecure-default / operator-misconfig) | phase-1 step 5; report-template |
| LLM/agent stochastic attacks as k-of-N evals, not single-shot | phase-2 LLM trust boundary |
| Framework-vs-app harness: minimal host, dist+symlink recipe | phase-2 Setup; harness-playbook |
| Cross-package / monorepo seams are the hunting ground | harness-playbook |
| Sibling sweep as mechanism; evidence = behavioral diff across siblings | phase-1 step 7; SKILL.md rule 5 |
| Supply-chain / fetched-executable-content class | phase-2 |
| Failure injection: concrete patterns (no blanket Proxy; indirect boundaries) | harness-playbook |
| Scanners-already-in-CI as step 0 | phase-1 step 0 |
| Packaging reality (declared min runtime vs stdlib used) | phase-1 step 6 |
| Test-suite hygiene sweep | phase-1 step 8 |
| Python target track (editable import, loopback hit-log, subprocess timeouts) | harness-playbook |
| Harness-lie gallery (worked self-inflicted false positives) | harness-playbook |
| Verification attribution (executed-by-me / independently-reconfirmed / constructed) | report-template |
| Workflow boundary: Phase 1 delegable; Phases 2/4 in main context | SKILL.md |

## Consequences

- Versioned skill: every future evolution has a diff; `claude plugin update`
  ships it.
- The gap lists from both assessments are fully incorporated and retired.
- Resist accreting this into a mega-checklist (phase-5 §4): new attack
  classes enter through invariants, not through a longer taxonomy.
