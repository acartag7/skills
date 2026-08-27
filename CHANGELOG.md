# Changelog

One section per skill. Each skill is versioned independently: per-plugin
semver in its `plugin.json` (mirrored in the marketplace entry), tagged
`<skill>--v<version>`, released per tag.

## hunt-consult

### 0.1.1 (2026-08-27)
- references/seats.md: verified one-shot launchers for the local cross-agent
  fleet (codex, grok, cursor-agent, claude, gateway claude) with the exact
  flags each failure cost a round to find; the gateway-env inheritance trap
  (a claude -p spawned from a gateway-routed session is the gateway model) and
  its env-strip recipe; the planted-discriminator round mechanism; the two
  round-2 patterns (execution-lift rerun, cite-or-concede dissent
  adjudication).

## adversarial-security-review

### 0.8.0 (2026-08-27)
- Harness-lie gallery +9 entries from a Level-3 SDK-monorepo engagement:
  ambient home-dir node_modules substitution, identity-vs-behavior verdicts,
  post-major-version API-drift cluster, never-ending SSE replay streams,
  zero-TTL serve-gates masking cache bugs, failure injection landing on the
  wrong call site, GC death-spiral starving the PoC's own instruments, two
  zsh traps (glob-abort, interactive-wrapper stdin).
- New release-tag verification rule: symlinked workspaces test HEAD, not the
  shipped artifact — per-package tag diff + registry dist-tags before the
  affected-versions line.
- Severity calibration +3 cross-agent-verified rules: one impact axis per
  consequence (no I:H riding a C:H theft), race-honest wording (invariant
  core vs race branch), AC disputes resolved by spec-cite and sibling-CVE
  precedent.

## hunt-kickoff

### 0.1.0 (2026-08-25)
- First cut, field-derived: compile entry state (board, policy dupe
  list, newest sync notes, git log) before any work; claim probe-ID
  blocks so concurrent lanes don't collide; emit a kickoff contract
  (goal as a board operation, immediate queue, run rules); convert
  vague starts into ranked candidate goals. Bootstrap path for fresh
  engagement repos: claim ledger, verbatim policy note, receipts
  convention (written in finally), authorization block, numbered
  notes with collision check. Closeout: board updates, sync note
  with instrument lessons, verdict-in-subject commit, hand off
  before compaction. Every rule traces to an observed failure or
  save in a six-day, 125-session agent-driven bounty engagement
  (mined by a 19-agent extraction pass).

## hunt-verdict

### 0.1.0 (2026-08-25)
- First cut, field-derived: evidence-class vocabulary
  (PROVEN-WITH-RECEIPTS / INADEQUATELY-TESTED / NOT-RUN /
  CLOSED-NEGATIVE / INSTRUMENT-BUG / RETRACTED); no negative without
  its same-run positive control; decode-before-judge on captured
  bytes; "target changed" requires the cheap falsifier first;
  fresh-create vs genuine-restore discriminator. Write path: edit
  ledger rows in place, re-read raw receipt bytes (auto-verdicts
  lie), supersession tombstones in superseded files, retraction
  propagation grep across all carriers, instrument-bug sweeps
  converting dependent verdicts to UNKNOWN. Filing gate: receipts-
  supported tier claimed, higher tier as argument with named
  missing oracle, sibling-draft check, same-day PoC re-run.

## hunt-consult

### 0.1.0 (2026-08-25)
- First cut, field-derived from 16+ archived rounds: round anatomy
  (authorization preamble, primary documents never digests,
  closed-lanes fence with receipt ids, own-holes section, one ranked
  question, anti-theater clauses, round-2 self-re-evaluation).
  Dissent injection (rival's verbatim disagreement pasted into the
  next round) and verdict back-relay as the two load-bearing
  mechanisms. Execution frozen (read-only, no subagents, single
  response, outside the repo); serving model verified from the
  response itself each round; prompt/answer archived as pairs;
  receipts-win triage with VOID-and-dated-correction for failed
  rounds; check-for-parallel-lane-files before calling fabrication.

## explainable-technical-writing

### 0.1.0 (2026-08-21)
- First release. Gives each file one Diátaxis mode, separates current reference from dated archives, requires exact public symbols and code-backed claims, and adds a teaching layer with examples, impact warnings, and diagrams.
- Includes reusable patterns for guarded actions, proxy-trust warnings, decision flows, complete examples, stable archive forwarding pages, and the final human explainability pass.

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

### 0.7.4 (2026-08-21)
- Field lessons from a live level-3 engagement against a sandboxed
  virtio target, each placed in the file that executes it. The
  harness-lie gallery gains stacked instrument closes (a "closed"
  verdict from one instrument is provisional, so vary the mechanism and
  the access width), per-boot dmesg, unprivileged false-denieds, the
  persistence-carrier probe, env-plumbing and SDK-arity traps, and
  binary bytes in socket logs. The playbook gains a marker-page
  observatory for memory-write claims, split-phase banking,
  reaction-attribution control, and detached instrumentation on managed
  sandboxes. The canary checklist item gains a negative control.
- Phase 1 gains version-delta lead harvesting: when the deployed version
  is pinned behind upstream, every "Fixed" changelog entry is an
  admitted bug the deployed code still carries. Phase 2 gains lifecycle
  primitives and state latches. Phase 3 gains teardown-usable and
  lifetime-coupling invariant seeds. Phase 4 gains seam H, the
  lifecycle interlock. The novelty floor promotes anomalies that
  contradict your own prior negatives to top-priority leads.
- Report template: Unproven rows split into missing-instrument vs
  unobservable-by-construction; severity calibration covers bounty
  tables; a new evidence-discipline section (isolated vs combined
  counts, decodes from the target's own tables, report = script =
  capture, identifiers must appear in the evidence).
- SKILL rules back to principle altitude after the intake: responder
  and handle identity, the baseline-attacker test, and staged-vs-proven
  labeling join the rule list. The two PoC-workspace rules merged into
  one.

### 0.7.3 (2026-08-20)
- Harness honesty, from a depth-3 engagement whose headline finding was
  retracted and falsified three ways. Exit-code capture binds to the
  target command, because a wrapper suffix reports the wrong exit. A
  probe that returns no body is a failed probe, not a blocked one.
  Reachability claims need evidence at the destination, with a positive
  control fired first. Dump and decode the wire before arming a byte
  patcher, because compressed framing makes patchers silent no-ops. A
  later-found-blind instrument converts its helds to unknowns. Managed
  runtimes reap non-detached instruments with their command session.
  Guest scripts go in files, not template strings. Stale scratch files
  lie.
- Taxonomy: the signature-scope oracle. One request tells you whether a
  captured signature authorizes a modified body, by whether the error is
  401 or a semantic 404.
- Phase 4: pattern G, unix-socket shadowing. Rename the socket, bind
  your own, proxy both directions, and you hold a MITM position on the
  platform channel. Also placement fingerprinting before multi-tenant
  isolation claims, and a composition allocation rule for when
  components individually hold.
- SKILL rule 10: the challenge-retest protocol. A retest is a redesign,
  not a re-run. The budget rule now covers quota pacing on API-metered
  targets.
- Editorial pass over every skill file: em dashes become sentences,
  semicolons become periods, prose slashes become "and" or "or",
  headings to sentence case, one thought per sentence. No rule, step, or
  pattern changed in meaning.

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
  taxonomy: primitive-layer contract mining, differential pairs, spec
  ambiguity, primitive properties (idempotence, canonical uniqueness),
  anomaly-first fuzzing, distant-domain transplants. May honestly
  produce nothing; novelty claims get no discount (executed, reduced,
  or downgraded).

### 0.6.1 (2026-08-15)
- Per-skill landing README. First tagged release
  (`adversarial-security-review--v0.6.1`).

### 0.6.0 (2026-08-15)
- Rules of engagement: target, authorization basis, and disposition
  stated before executing anything. It is methodology, and also what
  policy-gated models need to keep running authorized exploit work.
- Model-policy guidance in the operator note: pick a deployment whose
  usage policy covers offensive security testing; if a step is gated,
  drop depth or switch deployments. Never prompt around a refusal.
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
