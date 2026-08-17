# Repo-local review eval

A generic “be thorough” reviewer keeps signing CLEAN. Hosted
review then finds the same defect on the next adapter, leftover
sentence, or stored row. The fix is not another marketplace
plugin. It is a **skill that lives in the repo**, written from
that repo’s own review history, with evals frozen from heads
that already taught you what “good” looks like.

This page is how to build that on any project. The working
example lives in the originating repo, not in this marketplace.
No product name belongs here.

## What you are building

| Piece | Where | Job |
|---|---|---|
| Project skill | `.claude/skills/<name>/` in **that** repo | Exact-head local review. Empty matrix cell = FAIL. |
| Matrices | `references/matrices.md` next to the skill | **That** repo’s sibling axes, named. |
| Frozen evals | `evals/<id>/{prompt,graders}/` | Heads a pleasing CLEAN must refuse, plus one closed-class control. |
| Corpus index | `evals/CORPUS.md` | Maps real PRs → class → eval id. Gold stays in-repo. |

Do not ship the skill through `acartag7-skills`. The matrices
are the product. They are not portable as prose.

## What “good” is

The unit of review is a **behavior**, not a `file:line`.

- **Leftover sibling** — the named instance is fixed; the same
  rule is still false on another path. This is a review miss.
  Freeze it.
- **New class** — the contract never named the edge. This is
  STOP (amend the contract, re-cut), not another review round.
  Do not freeze it as a “reviewer should have known.”
- **Scope** — the slice was too big. Cut. Do not train the
  skill to endure a 20-round grind.

A good local pass **refuses** a CLEAN head and names the empty
cell. A pleasing pass agrees the named instance is done.

Keep one **control** head where the class really is closed, so
a review that always invents a blocker cannot farm the suite.

## 1. Mine the last 100+ PRs

In the target repo:

```bash
gh pr list --state all --limit 150 \
  --json number,title,state,createdAt,additions,deletions,url
```

For each PR, fetch reviews and inline comments (paginate). A
**round** is one hosted review on a distinct head SHA.

Score:

- rounds per PR
- extra rounds after the first
- size bucket vs mean rounds
- whether later rounds are the **same class** on a sibling

You already know the expensive shape if extra rounds stay high
on medium/large diffs while sub-150-line PRs stay at 0–1.

## 2. Name that repo’s sibling axes

Do not copy another repo’s adapter list. Walk the grinders and
write the rows that actually recurred.

Typical axes (replace the names):

- every HTTP / SDK / CLI surface that must stay equivalent
- every store / backend / replica
- library vs example vs generated starter vs scaffold
- new entry vs already-persisted vs in-flight
- throw vs result-object vs getter
- every page that still states the old guarantee

If you cannot fill a cell from the tree, it is empty. “Swept
siblings” with no cells is the failure mode you are replacing.

## 3. Write the repo-local skill

`.claude/skills/<name>/SKILL.md` — keep it short. Point at
matrices and an output contract.

Hard rules to copy (the wording can change; the refuse-to-PASS
must not):

1. Review the exact named commit. Do not edit.
2. Name the defective behavior in one sentence, then fill cells.
3. Empty applicable cell = FAIL.
4. A prior CLEAN is not evidence.
5. After a hosted finding, do not push until the **class** is
   closed. Fixing the reported line is the round multiplier.
6. New edge the contract never named → STOP.

Cursor: add a thin `.cursor/skills/<name>/SKILL.md` whose
description matches, and that tells the agent to follow the
canonical `.claude/skills/<name>/` tree. One set of matrices.

Wire the repo’s `AGENTS.md` (or equivalent) so local review
**before** hosted review is this skill, not a memory.

**Runner budget (do not weaken the matrices).** A strong
exact-head pass that mutates the shipped function is the
method. Ceremony is not. Put these next to the skill, not
instead of the cells:

1. After one environmental full-suite failure, classify the
   common shape and stop rerunning it.
2. Command output is summaries first. Full logs only for an
   unexpected failure.
3. No web or memory lookup unless the diff leaves a factual
   question unresolved.
4. An explicit token budget (40k–60k is a working default).
   Exceed it only after a genuine open class.

Classify an axis `n/a` the moment it cannot be a sibling of
the named behavior. “Exists in the checkout” is not a reason
to walk stores or schema. Emit the output contract once.

## 4. Freeze evals from the grinders

For each expensive leftover-sibling class:

1. Reconstruct a **tiny** exact head in `prompt.md` (a few
   files). Rename symbols if the marketplace-hygiene habit is
   in your fingers; in the product repo, real function names
   are better — they are the test.
2. Frame it as a CLEAN local pass. That is the experimental
   condition.
3. Grade the **class** and the **empty cell**, not a wording.
   FAIL if the review PASSes the head or only restates the
   instance the story already fixed.
4. Layout:

```text
evals/<id>/prompt.md
evals/<id>/graders/<id>.md
```

Start with six refuse-CLEAN heads plus one `class-closed`
control. Seed them from the classes that burned the most extra
rounds, not from a taxonomy you wish you had.

`evals/CORPUS.md` rows: PR, first extra-round class, eval id.
No need to paste the exploit. The gold is “which cell.”

When a hosted round finds a leftover sibling after this skill
said CLEAN, freeze a new case the same day. If you skip that,
the suite stops measuring the thing you care about.

## 5. Run the experiment

**Isolated CLI (required before you trust the skill).** Inline
the skill + matrices + output contract as the system prompt.
Run `claude -p` (or `codex exec`) with **no tools**, on the
pasted exact head only. Do not `--bare` if that skips login.
Grade with the grader, not the VERDICT line alone.

Iterate the skill until the suite is **≥6/7 (80%)**, including
the control. A suite that always FAILs is not 80% — the
control must PASS. Write the score in `evals/RESULTS.md`.

**Judgment eval (same gold, interactive).** Fresh session,
skill active, paste `evals/<id>/prompt.md`, grade with the
grader. A pleasing CLEAN must FAIL the six; the control must
PASS.

If the CLI can see project evals, use that. If it cannot, the
prompt + grader pair is still the experiment.

**Live head (optional, stronger).** Check out the real pre-fix
SHA from `CORPUS.md`, run the skill on that tree, grade the
same class. Use this when you want to know whether the skill
survives a full repo, not a reconstructed snippet.

**Other projects.** Repeat steps 1–4 in that repo. Do not
reuse another product’s matrices. Compare:

- extra hosted rounds after the skill is wired, vs the month
  before
- follow-up PRs that are only the next sibling of the last
  merge (those should go to zero)

The skill is working when hosted review **verifies** and stops
**discovering** the next cell.

## 6. What not to do

- Do not put unreleased vulnerability detail, third-party
  assessment targets, or personal paths in a public marketplace
  repo. Product-repo evals may name that product’s own public
  PRs.
- Do not freeze “review as spec discovery” as a skill success.
  That is a contract hole.
- Do not add a case for every comment. One case per **class**.
- Do not skip the control head.

## Origin

The refuse-to-PASS contract and the leftover-sibling classes
come from a 130-PR hosted-review measurement. ADR
[0005](adr/0005-class-closure-review-from-hosted-rounds.md)
records which gap became which rule. The working skill is in
the originating repo.
