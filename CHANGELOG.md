# Changelog

One section per skill. Each skill is versioned independently: per-plugin
semver in its `plugin.json` (mirrored in the marketplace entry), tagged
`<skill>--v<version>`, released per tag.

## adversarial-security-review

### 0.6.1 (2026-08-15)
- Per-skill landing README. First tagged release
  (`adversarial-security-review--v0.6.1`).

### 0.6.0 (2026-08-15)
- Rules of engagement: target, authorization basis, and disposition
  stated before executing anything — methodology, and also what
  policy-gated models need to keep running authorized exploit work.
- Model-policy guidance in the operator note: pick a deployment whose
  usage policy covers offensive security testing; if a step is gated,
  drop depth or switch deployments — never prompt around a refusal.
- Report header carries the engagement basis.

### 0.5.0 (2026-08-14)
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

### 0.4.0 (2026-08-14)
- Disposition is always an ask: PR (own repos) or private disclosure
  (third-party; suggested only for plainly bad/dangerous findings).
  Artifact disposition (keep / promote / attach / discard) is the
  operator's call.

### 0.3.0 (2026-08-14)
- Instrument honesty: no "held" without proof the instrument fired
  (checklist item 4, phase-2 rule, +4 harness-lie gallery entries).
- Durable evidence workspace with close-out copy (rule 8); Phase 5
  consumes the preserved copy, non-reproducing PoCs tagged as-recorded.
- Phase outputs are files that gate later phases; H1…Hn IDs bind Phase 4
  attacks to invariants.
- Plain-language report layer, Disconfirmed lane, third-party
  disclosure-note pattern.

### 0.2.1 (2026-08-14)
- Description leads with the executed-PoC essence (writes and executes
  real exploits and PoCs to verify or kill security hypotheses).

### 0.2.0 (2026-08-14)
- Phases 2–4 pinned to a single strong model at max effort; Phase 1 is the
  fan-out stage.

### 0.1.0 (2026-08-14)
- Initial import as an installable plugin (marketplace `acartag7-skills`).
