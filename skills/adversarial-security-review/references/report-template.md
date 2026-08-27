# Report template

```
# Adversarial security assessment: <target>
Depth level: <1, 2, or 3>. Phases run: <list>. Date.
Engagement: own repo, authorized scope, or good-faith coordinated disclosure.

## Summary (plain language)
One-sentence posture headline: what is genuinely strong, before any
finding. Then findings ranked by what actually matters, each in three
sentences: What it is, What actually happens, If it's not fixed. Close
with what was tried and disproved, in the same register.

## Confirmed
For each:
- **Vn, title** *(exploitable, latent with no current exploit, or
  reliability-not-security)*
- **Defect-type:** code-bug, insecure-default, or operator-misconfig. The
  tag drives the fix path (code change, docs plus guardrails, or runbook)
  and calibrates severity. A documented default that ships open is still
  a finding.
- **Verification:** executed-by-me with the PoC inline;
  independently-reconfirmed, meaning a second reviewer re-derived it from
  source; or constructed, meaning the injectable artifact was shown and
  the live backend was not run. Attribute honestly, and don't let a
  second party's *non*-verification downgrade your own executed evidence.
- Invariant: the assumption that failed.
- Mechanism, plus the exact state constructed.
- PoC: file and inline output, expected against observed.
- Severity reasoning: who can do what, gated by what.

## Unproven
Hypotheses with a real mechanism that couldn't be executed. For each, say
what was missing (no MySQL, egress not closed, needs a live tenant) and
exactly what would close it. Distinguish two sub-classes with different
weights: **missing instrument** (the harness lacked a dependency; adding
it closes the question) and **unobservable-by-construction from this
position** (the experiment ran repeatedly, but the effect destroys its
own evidence faster than any output channel escapes: the target dies and
takes the observation with it). The second is stronger. The mechanism is
source-verified and the effect is reproduced. Only the observation is
missing. Say which sub-class each Unproven row is.

## Held
Attacks that failed. For each, the specific interleaving or input ruled
out. This section is the backbone: it is the evidence behind the
confidence. "I reviewed the code" entries are forbidden here. Executed
attempts only.

## Disconfirmed (harness or expectation)
Outcomes that were neither Confirmed nor Held. Your expectation was
wrong, or the mechanism is real but operates one level up or over. The
defense earned no credit and the attack earned no finding. What changed
is the model of the system. Keep this lane separate from Held.

## Recommendation
Does anything change the standing usage recommendation, and for whom?
Separate exploitability findings from hardening notes and operational
concerns.
```

## Plain-language layer (write it first)

The full report is for engineers. The assessment also needs a rendering
the operator can read with no code open, so assume the report's first
reader is not deeply technical. Structure:

- One-sentence posture headline: what is genuinely strong, before any
  finding.
- Findings ranked by what actually matters, each in three sentences.
  **What it is**: the mechanism, without jargon-only nouns. **What
  actually happens**: the concrete attacker story. **If it's not
  fixed**: the consequence in operational terms.
- The calibration list in the same register: what you tried and
  disproved, so the reader knows the findings were executed, not
  guessed.

## Third-party disclosure note

When the target isn't yours, the final artifact is the private
disclosure, not the full report. It carries: good-faith framing (what
you were doing and why), the posture headline, ranked findings in the
three-sentence register with PoCs attached rather than inlined, the
disproved list for calibration, an offer to walk through the PoCs and
coordinate fixes, and a proposed window. Ninety days is usual, flexible
for conditional-reachability findings. Credit is their call. Nothing
goes public, findings or PoCs, until the fix ships. HELD cases and
fixed findings are the publishable ones.

## Severity calibration (be honest)

- **Exploitable.** An unauthenticated or low-privilege attacker gains
  something concrete today.
- **Latent.** An invariant is violated with no current exploit path.
  Still a finding, because it's a future exploit.
  Misconfiguration-gated issues go here.
- **Reliability.** Availability or semantics only: DoS, wedged
  sessions, status-code oracles. Say "not security" out loud.
- **Operational.** The code is right and the default or deployment
  pattern is the risk, such as opt-in limiters or unbounded
  unauthenticated writes.

**Bounty tables.** When the target publishes a bounty table, claim the
tier that matches the demonstrated impact, keyed to the specific row's
wording. Never claim a tier whose description you cannot quote verbatim.
Do not under-claim to dodge the inflation penalty either: the penalty
targets inflation "without a rationale aligned with the table", and when
the evidence supports the tier and the rationale is present, claim it.
When acquiring the capability depends on a conceded precondition, such
as a leak you could not demonstrate, say so in the severity paragraph
and claim one tier net of the concession. Let the triager's surprise be
an upgrade, not a downgrade.

## Evidence and capture discipline

- **Counts: isolated vs combined.** "The full sequence died N times" and
  "the killer step was isolated M times" are different claims. Report
  both, and never dress the combined count as the isolated one. A
  sequence can die five times while the killer step is isolated only
  once.
- **Decodes from the target's own tables.** Derive every bit or flag
  decode from the target's constant tables, source or authoritative
  headers, never from memory. Draft specs and UAPI disagree on bit
  positions, and a decode error can sustain a wrong narrative for
  hours. Re-verify any correction with the same skepticism as the
  original decode. A reviewer's counter-decode can carry its own
  transposed nibble.
- **Report = script = capture.** The reproduction section must match the
  attached script's commands and the attached capture's output. If the
  script ran a command the report omits, or the capture shows output the
  report trims, fix the report before submitting. A triager who opens
  the capture and finds more than the report claimed reads everything
  after that as suspicious. Quote evidence faithfully. Neutralize in a
  clause, never by omission.
- **Every identifier must literally appear in the attached evidence.**
  Harvest identifiers from the run's own capture file into the report as
  you prove each finding. Before submission, grep every session ID, team
  ID, project ID, sandbox name, and timestamp against the capture files.
  Identifiers from a different run, from a teammate's handoff, or from
  memory are the single most common way an otherwise-solid report gets
  read as hallucinated. If evidence spans multiple runs, list both runs
  with dates and map each claim to its run.

## The closing paragraph

State plainly what was attacked, how (executed, not read), what
survived, and what the negative result is worth. If nothing exploitable
was found, say that IS the deliverable, and say why the evidence
supports it. End with the disposition asks: Phase 5, meaning a PR on
your own repo or a private disclosure on third-party code, with
disclosure suggested only for plainly bad or dangerous findings. Then
ask what to do with the preserved artifacts.

## Severity-calibration rules — cross-agent verified (SDK monorepo, 2026-08)

Three rules that two+ independent model seats converged on, none of which the
original report got right unaided:

1. **One impact axis per consequence.** Follow-on use of a stolen credential is
   C:H; scoring it ALSO as I:H double-counts one event. "The attacker's own
   issued token was accepted by the attacker-configured server" is not
   integrity impact on a third system — it is the confidentiality event
   completing. Expect triage to re-score any I:H riding a C:H theft.
2. **Adversarial-verification wording must be race-honest.** A behavior
   observed once ("loop survives close()") may be the majority branch of a
   race, not a guarantee — one seat ran it 7 times and got 5/7. Run the
   deterministic core enough times to say which part is invariant (unbounded
   recursion, never-settling promise: 7/7) and which is a race (post-close
   survival: 5/7), and word the report accordingly.
3. **CVSS AC disputes resolve by spec-cite and precedent, not intuition.**
   "Requires the victim to have configured X" is NOT automatically AC:H:
   CVSS v3.1 §2.3.3 removed "presence of certain system configuration
   settings" from AC, and the User Guide has library scorers assume the
   reasonable worst-case implementation. The decisive move is
   precedent-shopping: find the published sibling CVE carrying the same
   precondition qualifier and check how NVD scored it. Also: Environmental
   considerations (rarity in the wild) do not move Base.
