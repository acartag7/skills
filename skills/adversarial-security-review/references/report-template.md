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
exactly what would close it.

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

## The closing paragraph

State plainly what was attacked, how (executed, not read), what
survived, and what the negative result is worth. If nothing exploitable
was found, say that IS the deliverable, and say why the evidence
supports it. End with the disposition asks: Phase 5, meaning a PR on
your own repo or a private disclosure on third-party code, with
disclosure suggested only for plainly bad or dangerous findings. Then
ask what to do with the preserved artifacts.
