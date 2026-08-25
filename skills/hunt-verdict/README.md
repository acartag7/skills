# hunt-verdict

A verdict that isn't written correctly is a future false finding. The
record rots in a specific way: the verdict flips only in the newest file
while every older carrier — notes, handoffs, report drafts — keeps the
dead claim standing. In the engagement this skill was mined from, a
retracted "the platform changed" narrative survived in the handoff the
next session read first, and a wrong oracle field silently invalidated a
whole lane's positives.

This skill is the write path for research verdicts, not a style guide:

- **Evidence classes before recording** — PROVEN-WITH-RECEIPTS /
  INADEQUATELY-TESTED / NOT-RUN / CLOSED-NEGATIVE / INSTRUMENT-BUG /
  RETRACTED. A negative without its positive control in the same run is
  NOT-RUN, not closed. A testing failure is never laundered into a
  negative.
- **Receipt hygiene** — the receipt must exist, parse, and contain the
  bytes the verdict cites; re-read the raw response, not the harness's own
  verdict string (auto-verdicts have lied).
- **Supersession tombstones** — a corrected verdict leaves a forward
  pointer in the file it supersedes, so a reader entering anywhere gets
  the truth.
- **Retraction propagation** — grep the corpus for the retracted claim
  and fix or annotate every carrier. A retraction that lives in one file
  is a landmine.
- **Instrument-bug sweeps** — when a harness bug is diagnosed, every
  prior verdict that instrument produced converts to UNKNOWN with a
  re-test note.

Filing-adjacent verdicts get the same discipline: severity claimed at the
tier the receipts support, the higher tier attached as argument with its
named missing oracle, and a fresh PoC re-run before anything is filed.
