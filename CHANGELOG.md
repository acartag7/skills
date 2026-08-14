# Changelog

## 0.5.0 (2026-08-14)
- Report template: plain-language summary moved INSIDE the template block;
  disposition asks close the Recommendation section; disclosure hygiene
  (nothing public until the fix ships).
- Subagent spine synced: Disconfirmed lane, no-"held"-without-fired-
  instrument clause.
- `phase1-leads.md` row schema pinned (example table) for mechanical
  validation of fan-out delegates.
- Secrets rule: PoCs carry ephemeral/local creds only; scrub before any
  promote or attach.
- Budget rule (9): per-phase PoC/timebox ceiling, stop-and-report.
- Close-out vault shape pinned: `<target>/<date>-level<N>/` + manifest.
- Eval suite (3 judgment cases: dead-instrument, downgrade-by-execution,
  race honesty) + CI manifest gate.
- De-tailored for public release: assessment targets genericized in ADRs.

## 0.4.0 (2026-08-14)
- Disposition is always an ask: PR (own repos) or private disclosure
  (third-party; suggested only for plainly bad/dangerous findings).
  Artifact disposition (keep / promote / attach / discard) is the
  operator's call. ADR 0003.

## 0.3.0 (2026-08-14)
- Instrument honesty: no "held" without proof the instrument fired
  (checklist item 4, phase-2 rule, +4 harness-lie gallery entries).
- Durable evidence workspace with close-out copy (rule 8); Phase 5
  consumes the preserved copy, non-reproducing PoCs tagged as-recorded.
- Phase outputs are files that gate later phases; H1…Hn IDs bind Phase 4
  attacks to invariants.
- Plain-language report layer, Disconfirmed lane, third-party
  disclosure-note pattern. ADR 0002.

## 0.2.1 (2026-08-14)
- Description leads with the executed-PoC essence (writes and executes
  real exploits and PoCs to verify or kill security hypotheses).

## 0.2.0 (2026-08-14)
- Phases 2–4 pinned to a single strong model at max effort; Phase 1 is the
  fan-out stage. ADR 0001 (field-assessment hardening provenance).

## 0.1.0 (2026-08-14)
- Initial import as an installable plugin (marketplace `acartag7-skills`).
