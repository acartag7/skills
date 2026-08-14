# ADR 0002 — Evidence-infrastructure hardening from the disclosure run

Status: accepted (2026-08-14)

## Context

A Level-3 run against a third-party open-source target ended in a
coordinated private disclosure (target, version, and findings intentionally
omitted here — unreleased vulnerability detail never enters this repo; the
assessment record lives in the operator's private vault, publishable only
per its policy: HELD or fixed). Post-run review surfaced four failures the
skill had no rule for: two near-shipped false "held" verdicts caused by
dead instruments (a guard keyed on a nonexistent `FileHandle.path`; a
hand-rolled DNS server writing ANCOUNT=0), evidence stranded in OS-purged
/tmp, phase outputs living only in chat scrollback, and reports legible
only with the code open.

## Decision

Four additions — all evidence-infrastructure; no phase semantics changed:

1. **Instrument honesty.** Interception counters > 0 and positive-control
   responses asserted before any "held" (playbook checklist item 4, phase-2
   rule); four new harness-lie gallery entries for the instrument-dead
   failure class.
2. **Durable evidence workspace** (SKILL.md rule 8): persistence probe at
   setup, close-out copy to a per-target assessment vault, named in the
   report; Phase 5 consumes the preserved copy and tags non-reproducing
   PoCs `as-recorded`.
3. **Phase outputs are files** (`phase1-leads.md`, `phase3-invariants.md`)
   that gate later phases via the Phases table's Input column; hypothesis
   IDs H1…Hn bind Phase 4 attacks to invariants mechanically.
4. **Report readability.** A plain-language layer (posture headline;
   what-it-is / what-actually-happens / if-it's-not-fixed; disproved list),
   a Disconfirmed lane distinct from Held, and a third-party
   disclosure-note pattern.

## Consequences

Evidence survives sessions and sandbox scoping; "held" requires the
instrument to have demonstrably fired; the report's first reader no longer
needs the code open. Phase-5's "PoCs → regression tests" promise now has a
guaranteed input instead of hoping /tmp survived.
