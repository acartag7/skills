# skills

Personal Claude Code plugin marketplace. Each skill lives in its own directory
under `skills/` and is an independently installable plugin, listed in
`.claude-plugin/marketplace.json`.

## Skills

| Plugin | What it does |
|---|---|
| [`adversarial-security-review`](skills/adversarial-security-review/README.md) | Break-the-product security assessment. Executed PoC evidence, not opinions — three depth levels from static claims review to invariant-first falsification. |
| [`contract-first-product`](skills/contract-first-product/README.md) | Design-session method: actor × state matrix, hostile tests that construct the named case, paste-ready implementation prompts. The design chat does not write product code. |

## Guides

| Guide | What it is for |
|---|---|
| [`repo-local-review-eval`](docs/repo-local-review-eval.md) | Turn a repo’s hosted PR review history into a **project** skill + frozen-head evals. Repeat on other repos. Not a marketplace plugin. |

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

## Releasing (per skill, independently versioned)

1. Run the eval suite if plugin evals are enabled for your CLI
   (`claude plugin eval skills/<name>` — currently early access, gated;
   cases live in `skills/<name>/evals/`).
2. Bump `version` in the skill's plugin.json AND its marketplace entry.
3. Add the `CHANGELOG.md` entry under the skill's section — hand-written,
   user-facing changes first.
4. Commit, push; CI validates manifests (strict) and version agreement;
   the evals job is manual (`workflow_dispatch`) and spends API credits.
5. `claude plugin tag skills/<name>` (validates manifest agreement) and
   push the tag it names.
6. `gh release create <skill>--v<version> --title "<skill> v<version>"`
   with hand-written notes.
7. Update the marketplace and plugin as above.

## Adding a skill

1. `skills/<name>/` with a `SKILL.md` (frontmatter `name:` controls the
   invocation name) and any `references/`.
2. `skills/<name>/.claude-plugin/plugin.json` — `name` is the only required
   field.
3. Add the entry to `.claude-plugin/marketplace.json` with
   `"source": "./skills/<name>"`.
4. Validate before pushing: `claude plugin validate .` and
   `claude plugin validate skills/<name>`.
