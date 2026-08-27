# Seat launchers — verified recipes for the local cross-agent fleet

Every entry burned a round before it worked. Flags are load-bearing. Verified
on the SDK pre-filing round (2026-08).

## The gateway-inheritance trap (read first)

A Claude Code session routed through an Anthropic-compatible gateway (GLM,
etc.) exports `ANTHROPIC_BASE_URL` / `ANTHROPIC_AUTH_TOKEN` /
`ANTHROPIC_DEFAULT_*_MODEL`. A `claude -p` spawned from that shell IS the
gateway model wearing Claude's name — an unstamped seat, the exact failure
this skill's model-identity rule exists for, caught live again. De-contaminate
with `env -u` for every one of those vars (plus `CLAUDE_CODE_*` session vars,
`CLAUDE_PLUGIN_DATA`, `CLAUDECODE`, `CLAUDE_PID`, `CLAUDE_EFFORT`,
`CODEX_COMPANION_SESSION_ID`) so the seat uses the real `~/.claude` auth. For
the deliberate gateway seat, extract the alias's env assignments from the rc
and `eval "export ..."` them (token stays out of command lines and logs),
launching the binary directly — never echo the token.

## Launchers

| Seat | Working invocation | Failure modes seen |
|---|---|---|
| codex | `codex exec --skip-git-repo-check --sandbox danger-full-access --cd <dir> "<prompt>" < /dev/null` | without `--skip-git-repo-check`: exits on untrusted-dir; open stdin: hangs on "reading from stdin". Its cybersecurity filter refuses exploit-verification content even with an authorization preamble first-line — two refusals = drop the lane, do not reword around it. |
| grok | `grok -p "<prompt>" --always-approve --cwd <dir>` | none — cleanest seat |
| cursor-agent (kimi) | `cursor-agent -p --trust --yolo --model kimi-k3 "<prompt>"` | without `--trust`: workspace-trust prompt kills headless; without `--yolo`: its config allowlist (`~/.cursor/cli-config.json` `approvalMode`) silently rejects every non-`ls` command — the seat will produce source-only analysis and MUST disclose that itself |
| claude (true Claude) | `env -u <gateway vars…> claude -p --allowedTools "Read,Grep,Glob,Bash,Write" < seat-prompt.txt` | prompt-as-arg + `-p` errors "input must be provided via stdin or arg" — stdin delivery works; Opus-5 refused security-verification content twice (preamble present) → dropped lane |
| gateway claude (glm) | alias env exported, then `claude -p --model <id> --settings <settings.json> --allowedTools … < seat-prompt.txt` | `zsh -ic 'alias…' < file` does NOT deliver stdin through the interactive wrapper; unknown-model is a WARNING not fatal (lane still answers — the unrecognized_model log line is noise unless output stops after it) |

## Round mechanics that earned their keep

- **Authorization preamble is the first line of the SEAT prompt**, not just
  the referenced CONSULT file — policy filters fire before the model reads
  anything else. A seat refused without it; another still refused with it:
  preamble-first is necessary, not sufficient.
- **Planted discriminator**: embed one known-false claim in the pack ("a
  colleague says schema X rejects javascript: — confirm or refute BY RUNNING").
  Executing seats must catch it by running; a seat that "confirms" without
  running is in digest-mode — void its round. A seat that statically derives
  the refutation AND discloses it could not run is honest — accept with marks.
- **Round 2 is two cheap patterns**: (a) execution-lift rerun — "your r1
  constraint is lifted; execute; would you change any verdict?" (b) dissent
  adjudication — paste the verbatim disagreement, demand cite-spec-or-concede
  in ≤120 words. Both produced material corrections (a race-rate amendment; a
  spec-cited AC reversal with NVD precedent).
- **Seat answer files + stdout log per seat**; archive prompt+answer pairs
  per round. Monitor for completion with `find -name` (zsh glob-abort), and
  remember `-p` seats print only at turn end — a quiet log is a running seat.
