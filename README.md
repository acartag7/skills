# skills

Personal Claude Code plugin marketplace. Each skill lives in its own directory
under `skills/` and is an independently installable plugin, listed in
`.claude-plugin/marketplace.json`.

## Skills

| Plugin | What it does |
|---|---|
| [`adversarial-security-review`](skills/adversarial-security-review/README.md) | Break-the-product security assessment. Executed PoC evidence, not opinions — three depth levels from static claims review to invariant-first falsification. |
| [`contract-first-product`](skills/contract-first-product/README.md) | Design-session method: actor × state matrix, hostile tests that construct the named case, paste-ready implementation prompts. The design chat does not write product code. |
| [`explainable-technical-writing`](skills/explainable-technical-writing/README.md) | Refactor dense technical docs into a clear, teachable system with exact symbols, code-backed claims, examples, impact warnings, diagrams, and archives. |
| [`hunt-kickoff`](skills/hunt-kickoff/README.md) | Session-start ritual for agent-driven security engagements: compile the board, claim the probe block, emit a kickoff contract. Bootstraps the scaffold in a fresh hunt repo. |
| [`hunt-verdict`](skills/hunt-verdict/README.md) | Ledger write path for research verdicts: evidence classes, receipt hygiene, supersession tombstones, retraction propagation, instrument-bug sweeps. |
| [`hunt-consult`](skills/hunt-consult/README.md) | External second-opinion rounds under a proven contract: primary documents, closed-lanes fence, dissent injection, serving-model verification, receipts-win triage. |
| [`bounty-report-craft`](skills/bounty-report-craft/SKILL.md) | Turn an executed finding into a filed report that survives adversarial triage — structural framing, realistic variants, multi-model review loop, capture discipline, venue gating. |
| [`finding-triage`](skills/finding-triage/SKILL.md) | Classify every executed finding before drafting and pick its venue (bounty / repo issue / hardening / drop): input-origin × boundary matrix, capability-equivalence check, closure-pattern catalog with logged predictions. |
| [`security-playbook`](skills/security-playbook/SKILL.md) | Meta-router for the security family: which skill runs, in what order, and the gates between them — triage before drafting, dupe pre-flight, rationed submission slots. Start here for any security task that spans skills. |

## Guides

| Guide | What it is for |
|---|---|
| [`repo-local-review-eval`](docs/repo-local-review-eval.md) | Turn a repo’s hosted PR review history into a **project** skill + frozen-head evals. Repeat on other repos. Not a marketplace plugin. |

## Install

Add the marketplace once, then install whichever skills you want (each is an
independently versioned plugin):

```
claude plugin marketplace add acartag7/skills
claude plugin install adversarial-security-review@acartag7-skills
claude plugin install security-playbook@acartag7-skills
claude plugin install finding-triage@acartag7-skills
claude plugin install bounty-report-craft@acartag7-skills
claude plugin install hunt-kickoff@acartag7-skills
claude plugin install hunt-consult@acartag7-skills
claude plugin install hunt-verdict@acartag7-skills
claude plugin install contract-first-product@acartag7-skills
claude plugin install explainable-technical-writing@acartag7-skills
```

The security set in one line:

```
for p in adversarial-security-review security-playbook finding-triage bounty-report-craft hunt-kickoff hunt-consult hunt-verdict; do claude plugin install $p@acartag7-skills; done
```

### Why skill names appear doubled (`adversarial-security-review:adversarial-security-review`)

Claude Code namespaces plugin skills as `<plugin>:<skill>`. Here each skill
IS its own plugin (so you can install and version them independently), so
the plugin name and skill name match and the listing shows both forms.
Invoking the bare name (`/adversarial-security-review`) resolves fine;
the doubled form is the fully-qualified one. If the doubling ever bothers
enough to restructure, the alternative is one multi-skill plugin
(`acartag7-skills:<skill>`) — at the cost of per-skill versioning, which
the release flow and validator currently enforce.

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
