# hunt-kickoff

An agent session that starts from a stale picture is worse than a blank
one: it re-derives known facts, contradicts verdicts landed hours earlier,
re-proposes leads you already killed, and drafts the report your other
lane wrote yesterday. In the engagement this skill was mined from, one
session in six existed mainly to answer "where are we?" — at 9 MB of
transcript a time.

This skill makes the entry state cheap and explicit. At session start it
compiles the current board (live findings, closed walls, retracted
tombstones, open queue), reads the program policy's dupe list, claims the
next probe-ID block so concurrent lanes don't collide, and emits a
one-screen kickoff contract before any work begins. A vague start
("check things") is converted into ranked candidate goals instead of
being filled with activity.

**Fresh engagement? It bootstraps the scaffold** — the claim ledger
(`BOARD.md`), the verbatim policy note, a receipts convention with
evidence written even when the probe dies mid-run, and the numbered-notes
habit — so day one starts with structure the source engagement took five
days and twenty re-orientation sessions to evolve.

Born from a full process-mining pass (19 extraction agents) over a
six-day, 125-session agent-driven bounty engagement: every rule in the
skill traces to an observed failure or save. Pairs with
`hunt-verdict` (the ledger write path) and `hunt-consult` (the external
second-opinion contract); each stands alone.
