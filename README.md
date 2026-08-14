# skills

Personal Claude Code plugin marketplace. Each skill lives in its own directory
under `skills/` and is an independently installable plugin, listed in
`.claude-plugin/marketplace.json`.

## Skills

| Plugin | What it does |
|---|---|
| `adversarial-security-review` | Break-the-product security assessment. Executed PoC evidence, not opinions — three depth levels from static claims review to invariant-first falsification. |

## Install (this machine)

```
claude plugin marketplace add acartag7/skills
claude plugin install adversarial-security-review@acartag7-skills
```

## Update flow

Edit the skill under `skills/<name>/`, bump `version` in that plugin's
`.claude-plugin/plugin.json` (and the matching marketplace entry), commit, push,
then:

```
claude plugin marketplace update acartag7-skills
claude plugin update adversarial-security-review
```

A session restart applies installed/updated plugins.

## Releasing

1. Run the eval suite if plugin evals are enabled for your CLI
   (`claude plugin eval skills/adversarial-security-review` — currently
   early access, gated; cases live in `skills/<name>/evals/`).
2. Bump `version` in the plugin manifest AND the marketplace entry.
3. Update `CHANGELOG.md` — hand-written notes, user-facing changes first.
4. Commit, push; CI validates manifests (strict) and version agreement;
   the evals job is manual (`workflow_dispatch`) and spends API credits.
5. Update the marketplace and plugin as above.

## Adding a skill

1. `skills/<name>/` with a `SKILL.md` (frontmatter `name:` controls the
   invocation name) and any `references/`.
2. `skills/<name>/.claude-plugin/plugin.json` — `name` is the only required
   field.
3. Add the entry to `.claude-plugin/marketplace.json` with
   `"source": "./skills/<name>"`.
4. Validate before pushing: `claude plugin validate .` and
   `claude plugin validate skills/<name>`.
