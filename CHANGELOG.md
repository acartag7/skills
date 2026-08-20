# Changelog

One section per skill. Each skill is versioned independently: per-plugin
semver in its `plugin.json` (mirrored in the marketplace entry), tagged
`<skill>--v<version>`, released per tag.

## class-closure-review

### withdrawn (2026-08-17)
- Not a marketplace plugin. The matrices are one repo’s review
  history; a portable plugin cannot name those cells without
  lying or leaking a product. The method is
  [repo-local-review-eval.md](docs/repo-local-review-eval.md).
  The working skill lives in the originating repo.

### 0.1.0 (2026-08-16)
- First cut: exact-head local review; empty matrix cell is FAIL;
  leftover claims, one-call-site wraps, stored-not-rechecked
  policy, guard-after-write, name-not-shape schema checks, and
  library-without-starter composition roots. Output contract
  PASS / FAIL / STOP. Freeze-a-case protocol for the next
  hosted miss. Seven evals (six CLEAN-must-fail heads plus a
  closed-class control). ADR 0005 records where each field
  gap landed.

## contract-first-product

### 0.1.0 (2026-08-16)
- First cut: ordinary words first; who can show up and in what
  state; talk.md for findings (people and rooms, one at a time);
  holes a freeze still lets through, including one rule hiding
  another; disclosure format; paste-ready prompts. Design chat
  does not implement.
- Evals a pleasing answer must fail: writer hides room;
  any-throw is not the guard; design tab does not code; talk
  plain; omit room on every tool. ADR 0004 records where
  each field gap landed.

## adversarial-security-review

### 0.7.3 (2026-08-20)
- Field-tested harness-honesty upgrades (from a full depth-3 engagement whose
  headline finding was retracted and three-way falsified): exit-code capture
  must bind to the target command (masked-exit wrapper lie); empty-body
  probes are failed probes; arrival-based evidence rule for reachability
  claims (observation at the destination, positive control first);
  dump-the-wire-before-patching (compressed framing makes byte patchers
  silent no-ops); blind instruments retroactively convert their helds to
  unknowns; managed-runtime lifecycle reaps non-detached instruments; guest
  scripts as files, not template strings; stale scratch files lie.
- Taxonomy: signature-scope oracle (one-request probe — does a captured
  signature authorize a modified body? 401-vs-404 semantics).
- Phase 4: pattern G — unix-socket shadowing (rename, bind, proxy both
  directions) as the acquisition primitive for platform-channel MITM;
  placement-fingerprint-first for multi-tenant isolation claims; explicit
  composition allocation rule when components individually hold.
- SKILL rule 10: challenge-retest protocol (retests are redesigns, not
  re-runs); budget rule now includes quota pacing on API-metered targets.

## adversarial-security-review

### 0.7.2 (2026-08-15)
- Plain-English explainer on the skill README: what "corpus" means, the
  three kinds of bugs, and why the novelty floor works (experiments and
  surprises, not creativity). Plain-terms box added to the floor itself.

### 0.7.1 (2026-08-15)
- Novelty floor reframed around generation-vs-selection: the corpus pins
  ingredients, not recipes; testing decides what may be claimed, never
  what may be generated. Operating principle: the world invents, the
  model reduces.

### 0.7.0 (2026-08-15)
- Novelty floor (Level 3): a reserved-budget mode for hunting below the
  taxonomy — primitive-layer contract mining, differential pairs, spec
  ambiguity, primitive properties (idempotence, canonical uniqueness),
  anomaly-first fuzzing, distant-domain transplants. May honestly
  produce nothing; novelty claims get no discount (executed, reduced,
  or downgraded).

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
