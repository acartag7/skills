# Report template

```
# Adversarial security assessment: <target>
Depth level: <1|2|3> — phases run: <list> — date

## Summary (plain language)
One-sentence posture headline: what is genuinely strong, before any
finding. Then findings ranked by what actually matters, each in three
sentences — **What it is / What actually happens / If it's not fixed**.
Close with what was tried and disproved, same register.

## Confirmed
For each:
- **Vn — title** *(exploitable | latent (no current exploit) | reliability, not security)*
- **Defect-type:** code-bug | insecure-default | operator-misconfig — drives the
  fix path (code change vs docs+guardrails vs runbook) and calibrates severity.
  A documented default that ships open is still a finding.
- **Verification:** executed-by-me (PoC inline) | independently-reconfirmed
  (a second reviewer re-derived it from source) | constructed (injectable
  artifact shown; live backend not run). Attribute honestly — and don't let a
  second party's *non*-verification downgrade your own executed evidence.
- Invariant: <the assumption that failed>
- Mechanism + the exact state constructed
- PoC: file + inline output (expected vs observed)
- Severity reasoning: who can do what, gated by what

## Unproven
Hypotheses with a real mechanism that couldn't be executed. For each:
what was missing (no MySQL / egress not closed / needs live tenant) and
exactly what would close it.

## Held
Attacks that failed. For each: the specific interleaving/input ruled out.
This section is the backbone — it is the evidence behind the confidence.
"I reviewed the code" entries are forbidden here; executed attempts only.

## Disconfirmed (harness or expectation)
Outcomes that were neither Confirmed nor Held — your expectation was wrong,
or the mechanism is real but operates one level up/over. The defense earned
no credit and the attack earned no finding; what changed is the model of
the system. Keep this lane separate from Held.

## Recommendation
Does anything change the standing usage recommendation, and for whom?
Separate: exploitability findings / hardening notes / operational concerns.
```

## Plain-language layer (write it first)

The full report is for engineers; the assessment also needs a rendering the
operator can read with no code open — assume the report's first reader is
not deeply technical. Structure:

- One-sentence posture headline: what is genuinely strong, before any finding.
- Findings ranked by what actually matters, each in three sentences:
  **What it is** (the mechanism, no jargon-only nouns), **What actually
  happens** (the concrete attacker story), **If it's not fixed** (the
  consequence in operational terms).
- The calibration list in the same register: what you tried and disproved,
  so the reader knows the findings were executed, not guessed.

## Third-party disclosure note

When the target isn't yours, the final artifact is the private disclosure,
not the full report: good-faith framing (what you were doing and why), the
posture headline, ranked findings in the three-sentence register with PoCs
attached rather than inlined, the disproved list for calibration, an offer
to walk through PoCs and coordinate fixes, and a proposed window (90 days
is usual; flexible for conditional-reachability findings). Credit is their
call. Nothing goes public — findings or PoCs — until the fix ships; HELD
cases and fixed findings are the publishable ones.

## Severity calibration (be honest)

- **Exploitable**: an unauthenticated or low-privilege attacker gains
  something concrete today.
- **Latent**: invariant violated, no current exploit path. Still a finding —
  it's a future exploit. Misconfiguration-gated issues go here.
- **Reliability**: availability/semantics only (DoS, wedged sessions,
  status-code oracles). Say "not security" out loud.
- **Operational**: the code is right, the default/deployment pattern is the
  risk (opt-in limiters, unbounded unauthenticated writes).

## The closing paragraph

State plainly: what was attacked, how (executed, not read), what survived,
and what the negative result is worth. If nothing exploitable was found,
say that IS the deliverable and why the evidence supports it. End with the
disposition asks: Phase 5 (a PR in your own repo vs a private disclosure —
disclosure suggested only for plainly bad/dangerous findings) and what to
do with the preserved artifacts.
