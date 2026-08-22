---
name: explainable-technical-writing
description: Refactor dense technical, protocol, security, and operator documentation into a clear Diátaxis structure with exact symbols, code-backed claims, examples, impact warnings, diagrams, and an archive for superseded evidence. Use for documentation systems that must teach readers without weakening technical precision. Do not use for marketing copy or cosmetic proofreading alone.
---

# Explainable technical writing

Make the documentation easy to enter, accurate enough to operate from, and structured well enough that a maintainer can explain it to someone else.

This skill governs the documentation system, not isolated sentences. Apply the repository's own writing and security rules too.

## Start from the real surface

Inventory every published documentation file, its inbound links, and its package or site inclusion. Read the source that enforces each behavioral claim before rewriting it. Include early returns, exception paths, and side-effect order.

Do not preserve a false statement for the sake of a docs-only diff. Report a code and contract mismatch as a defect. Change code only when the user authorized implementation.

## Give each file one job

Assign one Diátaxis mode to each file:

- A tutorial helps a learner complete one end-to-end path.
- A how-to guide helps an informed reader complete one task.
- A reference page states the current interface, values, limits, and results without persuasion.
- An explanation page teaches why the system has this shape and what alternatives would break.
- A history or archive page preserves dated evidence and superseded decisions. Put the date in each history heading.

Split mixed files at real reader questions. Keep stable forwarding pages when an old published path moves. Link the replacement and archive from both directions.

## Build a teaching layer

Keep reference pages dry, then link difficult subjects to explanation pages. A useful explanation answers these questions in this order:

1. What problem is this mechanism preventing?
2. Which exact actors, symbols, endpoints, or configuration fields take part?
3. What happens on the successful path?
4. What happens when a required action is omitted or configured incorrectly?
5. Why was this shape chosen over the obvious alternative?
6. What concrete example can a reader copy, run, or retell?

Use a small diagram when it makes three or more actors, stages, branches, or state changes easier to understand. Label nodes and edges with real symbols. Show the rejection branch when it explains the protection. Do not use a diagram as decoration.

For reusable patterns and examples, read [references/patterns.md](references/patterns.md).

## Name the thing

Use the actual public symbol or wire name: `POST /path`, `Type.method`, `CONFIG_FIELD=value`, or `Object.field`. Do not alternate between invented descriptions for the same thing.

Choose one term for each concept and run a repository-wide sibling sweep for old names, aliases, headings, links, examples, and archive entries. Keep an alias only when a reader must recognize an external or deprecated name, and label it once.

Do not expose implementation-batch labels, phase numbers, fix numbers, pull-request numbers, or review-session names in current user-facing documentation. Replace labels such as `S4a` with the capability they meant, such as "generic OIDC and Google." Keep an old label only in a dated archive when a reader needs it to interpret historical evidence. Specification requirement IDs, released versions, public symbols, and evidence commit hashes remain useful identifiers.

## Explain impact directly

State the actor and observable result. Prefer: "If the limiter throws, `POST /items` returns 503 before `Store.save`." Avoid: "Durable anonymous operations fail closed under enforcement-plane unavailability."

Use a GitHub admonition when missing the point can cause credential exposure, data loss, access bypass, an outage, or irreversible state:

```markdown
> [!WARNING]
> If the proxy trusts a client-reachable hop, a caller can choose the IP address used for rate limiting.
```

Use `IMPORTANT` for a non-obvious requirement that changes whether the procedure works. Do not put ordinary notes in admonitions. Punctuation is not a substitute for explaining the consequence.

## Make examples usable

Put commands and configuration in fenced blocks with copyable placeholders. Keep each prose paragraph on one physical source line unless the repository enforces another format. Do not insert arbitrary line breaks into commands, URLs, or sentences a reader will paste.

Show the smallest complete example. Name prerequisites, the expected result, and the important failure result. Verify every symbol against source and run the shipped entry point when the task permits it. Never invent an API because it looks plausible.

## Keep claims honest

Sweep every added or changed use of `always`, `never`, `cannot`, `only`, `must`, `safe`, `enforced`, `rejected`, and `guarantee`.

For each claim, record the enforcing symbol or test while working. Check sibling adapters, stores, providers, entry points, and stored-state reads. If the source only covers one branch, narrow the sentence to that branch. If the sentence describes intent instead of runtime behavior, replace it with the actual result.

Historical evidence proves only the named date, version, client, commit, and environment. Current reference pages keep the evidence relevant to the current release or source tree. Move superseded receipts to the archive without deleting them.

## Remove writing that hides the answer

Lead with the result. Use ordinary words before protocol terms, then give the RFC or specification name. Keep one thought per sentence when a sentence would otherwise need rereading.

Do not use em dashes. Do not use bold-label-then-dash lists. Cut throat-clearing, fake quotations, generic "key benefits," repeated conclusions, and claims that the design is robust or seamless without evidence.

Avoid replacing every dense paragraph with a table. Use a table for exact mappings and comparisons, a flow for sequence, a tree for hierarchy, and prose for reasoning.

## Review the whole reading path

Before handoff:

1. Start at the README and follow the path for a learner, deployer, operator, reviewer, and maintainer.
2. Check all relative links and heading anchors.
3. Confirm every current page points to relevant explanation and every archive page points back to current reference.
4. Run the terminology and claims sweeps.
5. Confirm examples use real symbols and have an expected result.
6. Confirm dangerous mistakes state who can do what or what breaks.
7. Ask whether a maintainer could explain each major mechanism from the docs without opening five unrelated files.

The human pass is part of the acceptance test. Treat "daunting," "I cannot explain this," and "I do not know which page is current" as documentation defects, not taste.
