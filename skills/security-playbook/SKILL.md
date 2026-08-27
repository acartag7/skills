# Security Playbook

Meta-router for the security skill family. When any security-research,
adversarial-review, or bug-bounty task arrives, this skill decides WHICH
family skill runs, in WHAT order, and what gate each stage must clear before
the next fires. It never executes findings itself — it routes and enforces
the pipeline.

Forged across the 2026-08 engagements: a Vercel duplicate closure that a
public-tracker dupe sweep would have caught, two Informative closures that
the venue matrix predicted, and a 45-cell adapter-family sweep whose yield
came entirely from one method. Every rule below exists because skipping it
cost money or a submission slot.

## The pipeline (canonical order — gates are hard)

```
hunt-kickoff                start or resume ANY engagement session
      │                     (compiles ledger + policy + probe IDs)
      ▼
adversarial-security-review the assessment itself
      │                     L1 static → L2 execute → L3 falsify
      │                     adapter families: concern × surface matrix
      │                     (references/unmirrored-sibling.md)
      ▼
/finding-triage             EVERY finding, BEFORE any drafting
      │                     venue matrix + closure prediction, logged
      │                     NOTHING reaches a bounty form without this
      ▼
bounty-report-craft         drafting the ones triaged "file"
      │                     report = script = capture, stable-version PoC
      ▼
hunt-verdict                when closure lands
                            records prediction match; calibrates the catalog
```

Side-channel, any time: `hunt-consult` — external-model second opinion
under the consultant contract (receipts win; never trust an unreviewed
delegate's output).

## Routing table

| The situation | Invoke |
|---|---|
| "continue", "where are we", "open lanes", or a fresh hunt repo | `hunt-kickoff` |
| "break this", "try to hack", "assess this repo", "pentest this library" | `adversarial-security-review` (ask depth if unstated) |
| The target has adapters/ports/multi-implementation anything | adversarial-security-review **with** the concern × surface matrix; read `references/unmirrored-sibling.md` first |
| A finding is executed and someone says "let's report it" | `/finding-triage` first — always, no exceptions |
| Triage says FILE and the report needs writing | `bounty-report-craft` |
| Triage says repo-issue / hardening / drop | no second skill; write the issue |
| Stuck on strategy, ranking under deadline, want a blind-spot check | `hunt-consult` |
| A closure lands (any state) | `hunt-verdict` — record prediction match or mismatch |
| Multiple findings waiting | triage ranks them; scarce submission slots go to the highest-confidence row, not the flashiest |

## Hard rules (pipeline-level, override nothing)

1. **PoC or it didn't happen.** No executed evidence, no finding. Static
   leads stay leads.
2. **Dupe pre-flight is a first-class gate** — before drafting anything,
   sweep the target's public advisories/CVE feed/issues. Where fixes are
   public (GitLab-style 90-day disclosure) this actually works; where they
   are private (H1 walls), bound the risk by the vulnerable code's birthday
   and say so in the report.
3. **Program intelligence before submission**: scope rows, exclusions,
   severity adjustments, beta windows — pulled fresh, not remembered.
4. **One vulnerability per report.** Siblings with one root cause file
   together only when triage says the program will consolidate anyway.
5. **Predictions are logged at triage and scored at closure.** A
   misprediction is new doctrine or a classification error — either way it
   goes back into this playbook's routing table.
6. **Submission slots are a rationed resource** (Signal systems, trial
   reports). Spend them like the last one: verified, executed, dupe-swept,
   triaged FILE — nothing less.

## When NOT to invoke the heavy pipeline

- Single-file code review with no trust boundary → plain review, no
  assessment skill.
- A question about a finding ("how bad is this?") → answer it; invoke
  skills only when work follows.
- Examples/docs-snippet issues, provider-package-only bugs → check the
  program's carve-outs before spending any stage on them.
