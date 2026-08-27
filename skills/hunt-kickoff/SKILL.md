---
name: hunt-kickoff
description: Session-start ritual for agent-driven security-research engagements (bug bounty, pentest, adversarial audit). Compiles current state from the engagement ledger + policy + newest notes, claims the next probe-ID block, and emits a kickoff contract before any work; bootstraps the scaffold (BOARD.md, notes/, policy) in a fresh engagement repo. Use when starting/resuming ANY engagement session, when the user says "continue", "where are we", "open lanes", or starts a new hunt repo.
---

# Hunt kickoff — assemble state before touching anything

Generalized from a 125-session mining of an agent-driven bounty engagement.
The most expensive failure class observed: sessions that started from a
stale or partial picture — re-derived known facts, contradicted same-day
verdicts, re-proposed dead leads, drafted duplicate reports. Target: under
5 minutes from session start to first real action.

## First run in a fresh engagement repo (bootstrap)

If no `BOARD.md` exists, propose and create the scaffold before research:

1. `notes/00-policy.md` — paste the program's ACTUAL policy text: scope,
   reward/severity table, known-findings dupe list, out-of-scope, disclosure
   rules. This is the constitution every later decision is calibrated
   against; a paraphrase is not acceptable.
2. `BOARD.md` — the claim ledger, with sections: Live filings / Confirmed
   (unfiled) / Closed walls (do-not-retest) / Retracted (do-not-resurrect) /
   Open queue / Platform-or-target drift / `NEXTID` counter. Row format:
   `ID | claim | verdict | receipt pointer | notes`.
3. A receipts convention — decide now where evidence lives
   (default `harness/out/` or `receipts/`), one JSON per probe, written in
   `finally`, unique filename per run.
4. An authorization block (one paragraph: program, account ownership,
   scope, coordinated disclosure) reused verbatim in every external prompt —
   models refuse security content without it.
5. Numbered `notes/NN-*.md` for anything durable; check prefix collisions
   before allocating (manual numbering collapsed twice in the source
   engagement).

If the repo already has an engagement CLAUDE.md, read it first — it wins.

## Every session start (new, resumed, or "where are we?")

1. **Read, in order**: engagement CLAUDE.md (if any) → `BOARD.md` →
   `notes/00-policy.md` → newest sync files (`ls -t notes/ | head -8`) →
   `git log --oneline -15`.
2. **Compile the entry state** into one screen: LIVE (findings + pending
   user actions) / OPEN queue / walls that constrain today's ideas /
   retracted list / drift warnings.
3. **Claim resources**: take the next ID block from BOARD's NEXTID, record
   the claim in a notes sync file (concurrent sessions must see it), bump
   the counter.
4. **Emit the kickoff contract** before starting: one-line goal (a board
   operation: "close X", "receipt the Y oracle", "draft report Z"), the
   immediate queue (1–3 concrete actions with paths), today's run rules
   (budget, target caps, disclosure). A vague start gets converted into
   2–3 ranked candidate goals the user picks from — never filled with
   activity.

## Rules carried into the session

- Every probe: controls beside tests, receipt in `finally`, run from the
  directory the harness expects (wrong cwd = silent auth failure).
- Identity-check every "reached/proved/blocked" claim the way its class
  demands (peer-cert / handle-name / external arrival witness).
- No cleanup automation that can touch live experiments (a cron destroyed
  running experiments in the source engagement); cleanup scopes to owned
  resources only.
- If context runs low: hand off BEFORE compaction — write the sync note and
  update BOARD while context is still good.

## Session closeout (before ending, or on "write a handoff")

1. Update BOARD rows for every verdict produced or changed; bump NEXTID.
2. Write/refresh the session's sync note: verdicts with receipt pointers,
   corrections to prior records, open threads with their exact next line of
   work, instrument lessons (each cost a probe — write it down).
3. Commit the session's files with the verdict in the subject; uncommitted
   notes have no safety net.
4. Durable changes (filings, walls, target drift) → update the engagement
   memory if one exists.
