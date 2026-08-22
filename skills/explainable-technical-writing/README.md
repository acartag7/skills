# explainable-technical-writing

Dense protocol docs often contain the right facts in the wrong shape. Current behavior, old test receipts, policy, implementation history, and operator steps end up in one file. A reader cannot tell what is current, what to copy, or why a guard exists.

This skill refactors the documentation system. It gives each file one Diátaxis mode, keeps exact reference separate from explanation, traces strong claims to code, adds copyable examples and impact warnings, uses diagrams for real branching, and archives superseded evidence without deleting it.

The acceptance test is human: a maintainer can find the current answer, explain the mechanism to someone else, and state what breaks when a required action is skipped.

## Install

```bash
claude plugin marketplace add acartag7/skills
claude plugin install explainable-technical-writing@acartag7-skills
```

## Contents

- `SKILL.md` contains the documentation workflow and review standard.
- `references/patterns.md` contains patterns for explanations, warnings, diagrams, examples, and archive forwarding pages.

This skill is versioned independently as `explainable-technical-writing--v<version>`. See the root [changelog](../../CHANGELOG.md).
