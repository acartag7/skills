---
name: hunt-consult
description: Run an external-model second-opinion round for a security-research engagement under a proven consultant contract — authorization preamble, primary documents, cite-or-abstain, closed-lanes fence, dissent injection, model-identity verification, archiving, receipts-win triage. Use when the user says "ask <model>", wants a second opinion, a blind-spot check, ranking under deadline, or an adversarial audit of the board.
---

# Hunt consult — the external second-opinion relay

Generalized from 16+ archived rounds across a mined engagement.
Consultants paid for themselves exactly when this contract was followed and
burned rounds when it wasn't: digest-fed rounds hallucinated; unstamped
rounds turned out to be the same model wearing a different label;
agreement-only rounds produced theater. Every element here is scar tissue.

## Round construction (in this order)

1. **Authorization preamble** — first line, always: the engagement's
   authorization paragraph (program, account ownership, scope, coordinated
   disclosure, impact-assessment purpose). Stronger models refuse or stall
   on security content without it.
2. **Primary documents, never prose digests** — paste the actual policy
   text, the BOARD, and verbatim receipt blocks. Digests make models
   hallucinate confidently (receipted twice in the source engagement).
   Keep the pack under ~100KB; auto-truncate stable material, keep the
   delta since the last round.
3. **The fence** — explicit lists: CLOSED (do not re-propose, with receipt
   ids) / RETRACTED (do not resurrect) / already-planned (do not repeat; I
   need what's MISSING). Without the fence every fresh context re-sells
   dead vectors.
4. **Holes section** — "what we have NOT done / weaknesses in our own
   negatives", stated by us first. This is where the real answers live.
5. **Numbered questions + output contract** — ONE ranked question when
   deciding ("FIRST/SECOND/THIRD" forces expected-value arithmetic).
   Standing clauses: "mark inference vs fact"; "cite the document for
   every claim or write NOT IN EVIDENCE"; "'explore more' is not an answer
   — 'file X at tier Y with framing Z' is"; "be adversarial about my list
   too — mark what should collapse into known classes"; round ≥2: "would
   you change any of your previous recommendations?" (cheapest calibration
   extractor observed).
6. **Dissent injection** — if another model answered, paste its verbatim
   disagreement into the next round. Agreement-only rounds converged on
   wrong answers; pasted dissent was the single biggest correction trigger.

## Execution

- Run from `/tmp` (no repo file access), read-only ask mode, **no
  subagents**, single response; multi-final/streaming-degraded output =
  failed round — discard and re-run.
- **Verify which model actually served** from the response's own metadata
  before labeling the archive. This is not paranoia: one engagement ran
  ~10 rounds of "cross-vendor" review served end-to-end by a single model
  because a local CLI routed differently than its label claimed. Vet each
  lane once per engagement with a discriminating smoke test (have it
  refute a known-wrong inference), and re-verify whenever tooling changes.
- Archive BOTH files per round — `assessments/consults/rNN-<slug>-prompt.md`
  and `-answer.md` (orphan answers are unattributable) — plus a README
  one-liner and the model stamp.

## Triage (never skip)

Receipts win: extract the answer's atomic claims; check each against
BOARD/receipts; record kept / refuted / corrected per claim in the archive
note; fold keeps into the ledger via the verdict write path. A round
contradicted by receipts gets VOIDed with a dated correction header — and
before calling anything hallucination, check whether the consultant
legitimately read a parallel lane's files (list the receipts dir for fresh
files first): one "fabrication" verdict was actually the sibling lane's
real receipts.

## Feedback loop

When a recommendation gets tested, relay the true outcome back in the NEXT
round's prompt ("VERDICT: none of the above; it was my harness"). This is
what turns a consultant from an idea generator into a calibrated
instrument — and it humbles the ones that were wrong.
