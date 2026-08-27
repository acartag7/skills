---
name: hunt-verdict
description: Record, change, or retract a research verdict in an engagement's claim ledger (BOARD.md) with receipt linkage, supersession tombstones, and stale-carrier propagation. Engagement-agnostic (bug bounty, pentest, audit). Use whenever a probe lands, a claim changes status, a finding is retracted, an instrument bug is discovered, or a prior record is corrected.
---

# Hunt verdict — the ledger write path

A verdict that isn't written correctly is a future false finding. In the
source engagement, verdicts flipped only in the newest file while older
carriers kept the dead claim — one retracted "the platform changed"
narrative survived in the handoff the next session read first.

## Before recording any verdict

Name its evidence class:
<b>PROVEN-WITH-RECEIPTS</b> (control fired, oracle verified) /
<b>INADEQUATELY-TESTED</b> / <b>NOT-RUN</b> / <b>CLOSED-NEGATIVE</b>
(control fired, target refused) / <b>INSTRUMENT-BUG</b> (harness lied —
recorded as such, never as target behavior) / <b>RETRACTED</b>.

- A negative without its positive control in the same run is NOT-RUN, not
  closed.
- A "closed" surface with three stacked failures on it gets one re-test
  with a different mechanism before the verdict stands.
- Decode before judging captured bytes (framing/compression layers have
  produced two-generation false negatives).
- "The target changed" requires the cheap falsifier FIRST: version/digest
  compare + byte-diff of your own request builders. One entire "patch
  narrative" in the source engagement was the agent's own script bugs.
- Fresh-create vs genuine-restore discriminator (marker write/read) in any
  snapshot/resume test — the single most productive false-finding class.

## The write

1. **BOARD row** — edit in place (never append a duplicate): verdict,
   receipt pointer, date, one-line why. New claim → new row.
2. **Receipt hygiene** — the receipt must exist, parse, and contain the
   bytes the verdict cites. Re-read the raw response field, not the
   harness's own verdict string — auto-verdicts have lied. Unique filename
   per run; never overwrite.
3. **Supersession** — replaces an older verdict: BOARD gets the new verdict
   + pointer; the superseded NOTE/report gets a one-line correction header
   in place ("SUPERSEDED by notes/NN — overturned by <receipt> because …").
   Replacements without tombstones lie to the next reader.
4. **Retraction propagation** — `grep -rn "<claim>"` across notes/, 
   reports/, handoff files; fix or annotate every carrier. A retraction
   that lives in one file is a landmine.
5. **Instrument-bug sweep** — when a harness/instrument bug is diagnosed,
   enumerate every prior verdict that instrument produced (same script
   family / oracle field / capture path) and convert dependent results to
   UNKNOWN with a re-test note.
6. **Commit** with the verdict in the subject; sync note + memory when the
   fact is durable (filings, walls, target drift).

## Filing-adjacent verdicts

- Severity claimed at the tier the receipts support; the higher tier
  attached as argument with its named missing oracle ("Critical IF
  <decisive evidence> receipts"). Overclaiming is priced by most programs
  (and by triage credibility).
- Before drafting any report: check for a sibling lane's draft on the same
  finding; check the policy dupe list; re-run the PoC fresh the same day
  (target surfaces harden mid-disclosure).
