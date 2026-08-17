# skills — operating notes

Public Claude Code plugin marketplace (`acartag7-skills`). One installable
plugin per skill under `skills/<name>/`, listed in
`.claude-plugin/marketplace.json`. Installs pin to this repo — there is no
other distribution channel, by design.

## Structure

- Each `skills/<name>/` is a self-contained plugin: its own
  `.claude-plugin/plugin.json`, its own `README.md` (landing paragraph,
  problem-first, like `adversarial-security-review`'s), `SKILL.md`,
  `references/`, optional `evals/`.
- A new skill enters as: directory + plugin.json + marketplace entry
  (`"source": "./skills/<name>"`) + row in the root README table + section
  in `CHANGELOG.md`. Validate before pushing:
  `claude plugin validate . --strict && claude plugin validate skills/<name> --strict`.

## Versioning & release (per skill, independent)

The repo has no version of its own. Per skill:

1. Bump `version` in the skill's `plugin.json` AND its marketplace entry.
   `plugin.json` is authoritative — users only receive updates on bump.
2. Add the `CHANGELOG.md` entry under that skill's section (hand-written,
   user-facing changes first).
3. Conventional commit, push. CI validates manifests (strict) and
   version agreement.
4. `claude plugin tag skills/<name>` → creates `<skill>--v<version>`,
   validating manifest agreement; push that tag.
5. `gh release create <tag> --title "<skill> v<version>"` with
   hand-written notes.
6. Locally: `claude plugin marketplace update acartag7-skills && claude
   plugin update <skill>@acartag7-skills`; a session restart applies.

A skill reaches 1.0.0 only as a stability promise for its method:
field-proven across real assessments AND its eval suite actually passing.
(`claude plugin eval` was CLI-gated early access as of 2026-08; eval cases
ship regardless.)

## Public-repo hygiene (enforced — learned the hard way)

- NEVER place unreleased vulnerability detail, assessment-target names, or
  local/personal paths in any file here. Findings live in the operator's
  private assessment vault; a target is named only after its disclosure
  resolves.
- Pre-push audit for assessment-derived content: grep for target names,
  commit SHAs, and versions; genericize examples to mechanism level (the
  harness-lie gallery entries are deliberately target-free).
- A history rewrite does NOT purge already-pushed commits — dangling SHAs
  stay fetchable on GitHub. The guaranteed purge is delete + recreate the
  repo (done once, 2026-08-15, for exactly this reason).
- Conventional commit subjects; no AI mentions or co-author trailers.

## Skills here

- `adversarial-security-review` — executed-PoC security assessment; see
  its README. Design history: `docs/adr/`.
- `contract-first-product` — contract-first design session; see
  its README. Birth notes: `docs/adr/0004-contract-first-from-a-design-session.md`.
- Repo-local review eval (not a plugin) — how to turn hosted PR
  history into a project skill + frozen evals, including on other
  repos: `docs/repo-local-review-eval.md`. ADR 0005.
