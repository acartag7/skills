---
name: adversarial-security-review
description: Adversarial security assessment that writes and executes real exploits and PoCs to verify or kill security hypotheses. The codebase gets broken, not reviewed. Three depth levels: (1) claims-vs-code static review, (2) executed attack-taxonomy PoCs, (3) invariant-first falsification under concurrency and partial failure. Use when the user asks to "break this", "try to hack", "falsify the security claims", "penetration-test this library", or wants a security assessment beyond a code read. Supports running individual phases and choosing a depth level.
---

# Adversarial security review

Break-the-product security assessment. The deliverable is **executed
evidence**, not opinions. Every claim, positive or negative, needs a PoC
that ran.

## Choosing depth

Ask the user if the depth is not stated. Otherwise infer it from their
wording:

| Level | Name | What runs | When |
|---|---|---|---|
| 1 | **Review** | Phase 1 only. The output is the lead list: hypotheses with file:line, marked not-yet-falsified. | quick audit, no code execution wanted |
| 2 | **Execute** | Phases 1 and 2 | "try to break it", standard adversarial assessment |
| 3 | **Falsify** | Phases 1 to 4 | "assume it's secure and prove it wrong", novel-bug hunting |

Higher levels include lower ones. Level 3 is the only level that can
honestly support a "safe to use" recommendation, and only when the
security-critical dependencies were actually executed, not modeled. If the
real store, IdP, or adapter could not be stood up, say the depth was capped
by the harness, not by the method. A Level-3 badge over modeled dependencies
is overclaiming.

Level 3 also carries the **novelty floor**: a reserved-budget mode for
hypotheses below the known taxonomy (primitive contracts, differential
pairs, spec ambiguity). Its absence of findings is not evidence of absence.
Its findings carry no novelty discount: executed, reduced, or downgraded
like anything else.

## Rules of engagement (state before executing anything)

One short block, up front. It frames every later step:

- **Target and commit** being assessed.
- **Authorization basis**: your own repo, an authorized engagement or bounty
  scope, or good-faith independent research intended for private
  coordinated disclosure.
- **Disposition intent**: report, PR, or disclosure. Phase 5 is always an
  ask.
- PoCs stay in the local workspace. Nothing touches production systems or
  third-party infrastructure.

This is methodology, not ceremony. It is also what policy-gated models need
to keep executing exploit PoCs without stalling: authorized good-faith
security research is permitted usage, and the framing must be TRUE. If a
step is still gated, do not prompt around the refusal. Drop to a lower
depth (static review), or run on a deployment enrolled in the provider's
cybersecurity program for advanced offensive work.

## Phases (each independently runnable)

| Phase | File | Input needed |
|---|---|---|
| 1. Static claims review | `references/phase-1-static-review.md` | repo access |
| 2. Taxonomy execution | `references/phase-2-taxonomy.md` | `phase1-leads.md` written; runtime and deps installable |
| 3. Invariant extraction | `references/phase-3-invariants.md` | Phase 1 done |
| 4. Seam falsification | `references/phase-4-seams.md` | `phase1-leads.md` and `phase3-invariants.md` written; Docker for real stores and DBs |
| Novelty floor | `references/novelty-floor.md` | Level 3, with or after Phase 4; reserved budget slice; may honestly produce nothing |
| 5. Close the loop | `references/phase-5-close-the-loop.md` | always an ask: PR on an owned repo, or disclosure on third-party code; needs the preserved close-out workspace |
| Harness patterns | `references/harness-playbook.md` | referenced by 2 and 4 |
| Advisory residuals | `references/advisory-residuals.md` | during Phase 1 (lead generation) and pre-drafting (dedup pre-emption) |
| Program intelligence | `references/program-intelligence.md` | before drafting any bounty submission: scope, exclusions, beta windows |
| Unmirrored-sibling patterns | `references/unmirrored-sibling.md` | during Phase 1 step 8 (adapter families) and Phase 4 pattern D; the strongest filing shape |
| Finding triage | `../finding-triage/SKILL.md` | after execution, before drafting: venue + close-risk prediction per finding |
| Report format | `references/report-template.md` | final output |

This is a continuous security-engineering loop, not a one-off pentest.
Disposition is ALWAYS an ask, never automatic. After reporting, ask: on a
repo the user owns, open a PR with the regression tests and the invariant
registry (Phase 5). On third-party code, suggest a private disclosure only
when a finding is plainly bad or dangerous, never as a default nudge. Also
ask what to do with the preserved artifacts: keep, promote, attach, or
discard. The user decides.

If the user asks for a specific phase ("just do the race-condition PoCs",
"skip straight to falsification"), run only that phase and its
prerequisites.

## Non-negotiable rules (all levels)

1. **No PoC, no finding.** State the hypothesis and the expected result
   before running. Report expected against observed. A disconfirmed
   hypothesis goes in "Held": it is evidence, not failure. **Level-1
   carve-out.** At the static-only level, an unenforced claim or fail-open
   path found by reading is reportable as a *hypothesis* with `file:line`,
   explicitly marked "not-yet-falsified". It may never be labeled Confirmed
   without execution.
2. **The unit of attack is a sequence under concurrency or partial
   failure.** Never a single request. Interleave operations. Kill a
   dependency between two of them. Ask what the third observes.
3. **Never model what you can execute.** If a claim needs MySQL, run MySQL
   (Docker). If you can't, mark the finding "unproven" and say exactly what
   would close it. Do not let the harness lie.
4. **Fix the harness when it lies.** A harness bug that looks like a finding
   (wrong Content-Length, redacted strings, collapsed headers) must be
   debugged to ground truth before reporting. A "closed" verdict from one
   instrument is provisional. Before writing off a surface, re-test it
   with a different mechanism and a different access width. The
   harness-lie gallery in `references/harness-playbook.md` carries the
   class list. Every entry there was caught live.
5. **Sibling sweep is exhaustive grep, never an eyeball pass.** A bug in one
   adapter, store, or path is a hypothesis about ALL of them, not a
   finding. Coverage evidence is a behavioral diff: the same crafted input
   through each sibling's guard. A grep showing one calls it and another
   doesn't is not coverage. **Strongest authorization evidence shape.**
   When every sibling endpoint enforces the control and the target
   endpoint doesn't, build the table and cite the status codes: "the
   platform knows how to do this, and this one surface is missing it"
   beats "bearer tokens are bad".
6. **PoCs live outside the repo, in a durable, named workspace**
   (`/tmp/<repo>-poc/`). Never commit exploit code. Never modify the
   target repo during assessment. Verify at setup that the path persists
   across commands: write a probe file and read it back in a second
   command, because sandboxes sometimes scope /tmp per-call. At close-out,
   copy the workspace into a per-target assessment vault
   (`<target>/<date>-level<N>/` with a manifest index). The vault holds
   every PoC plus `phase1-leads.md` and `phase3-invariants.md`; name it in
   the report's closing paragraph. That close-out copy is Phase 5's
   required input. Without it, "PoCs become regression tests" is
   unrecoverable. Then ASK the user what to do with the artifacts: keep,
   promote to regression tests, attach to a disclosure, or discard. Never
   dispose of them unilaterally.
7. **Reason from the system's invariants, not a CVE corpus.** Known-taxonomy
   coverage does not count as a novel finding. A violated invariant with no
   current exploit path does count: it's a latent bug.
8. **State the budget before starting.** Per phase, agree a PoC count or
   timebox ceiling up front. When it is spent, stop and report exactly what
   was covered and what the budget didn't reach. An unbounded falsification
   loop is a failure mode, not thoroughness. Budget includes QUOTA on
   API-metered targets (concurrency caps, async resource drain after
   cleanup, boot races, hibernation killing background processes). Pace one
   instance at a time with drain waits, and treat a mid-battery rate-limit
   as a pause, never as a result.
9. **A challenge is a retest, and a retest is a redesign, not a re-run.**
   When a finding is challenged, by the user or by your own doubt:
   1. Re-audit the HARNESS before re-auditing the target.
   2. Rebuild the instrumentation so the original false signal cannot
      recur: bind exit codes to the command, print bodies, count
      instrument firings.
   3. Add one discriminator per alternative explanation. For example,
      "connected then reset" and "filtered before connect" need different
      observables, or evidence at the destination.
   4. Prefer arrival-based evidence.
   Re-running the same PoC that produced a false positive re-produces the
   false positive.
10. **"A response arrived" is not evidence of who responded.** A status
    line plus `rejectUnauthorized: false` proves an HTTP server answered,
    never which one. A transparent proxy or CDN may answer for the
    destination, so check the TLS peer certificate's subject and issuer
    before claiming "reached X". When claiming "the target object did Y",
    verify the returned handle matches the expected object by name, ID,
    or team. An SDK that silently creates a new object instead of
    returning the requested one makes a same-credential test look like a
    cross-credential test. Any "reached X" or "acted on Y" claim needs a
    positive control that verifies identity, not just reachability.
11. **Run the baseline-attacker test before claiming a finding.** Ask:
    "What could this attacker already do without this bug?" If the answer
    includes the finding's impact, the bug is not a finding. This test
    killed more false positives in live engagements than any other single
    check. Apply it to every severity claim, not just to the finding
    itself.
12. **Label authored evidence as staged, not proven.** A leak vector that
    depends on a line of code the researcher wrote, such as a console.log
    that prints a credential or a command that was deliberately invoked,
    must be labeled "staged, not emitted by default". The evidence still
    demonstrates consequence, but the report must not imply the platform's
    own tooling produced the leak. Conflating the two is the
    credibility-killer on triage.

## Delegating to a subagent (copy-paste prompt core)

When handing a phase to a subagent, give it the target path, the depth
level, the phase file or files to follow, and this spine:

> Every reported issue needs an executable PoC. Static-level leads may be
> reported as file:line hypotheses marked not-yet-falsified, never as
> confirmed. Before each PoC, state the invariant, the hypothesis sequence,
> and the expected secure behavior. Then run it and record the observed
> behavior. Sequential awaits are not races. Verify real interleaving, or
> force contention with a lock, before using the word "race". Prefer
> sequences under concurrency, partial failure, retry, cache reuse, config
> transitions, and multi-replica execution over isolated malformed
> requests. Use real dependencies. Mark models as unproven. When a result
> surprises you, falsify the harness first. Sweep sibling implementations.
> Report Confirmed, Unproven, Operational, Held, or Disconfirmed. No "held"
> without proof the instrument fired (interception count > 0, or a positive
> control first). The objective is not finding count. It is concrete states
> where a security assumption stops holding.

**Workflow and orchestration boundary.** Fan-out hygiene, learned from a
wedged run: cap every schema array (`maxItems`) AND the prompt counts
("at most N leads") — max-effort agents stall emitting one uncapped
StructuredOutput. Workflow journal completion events are `{"type":"result"}`;
grepping for "completed" reports zero. **Workflow and orchestration boundary.** Phase 1 (read-heavy fan-out whose
output is schema-verified and adversarially re-checked against source) is
safe to delegate to a workflow or subagents. Phases 2 and 4 stay in the
MAIN context. PoC execution and falsification are where harness-honesty
catches plausible-but-wrong results, and a delegate eager to please
produces worse evidence exactly there.

## Operator note: what runs this well

The bottleneck is not exploit-writing. Taxonomy PoCs are pattern-matched
and easy. The bottleneck is **self-skepticism under a plausible-looking
success**. The model running this skill must extract invariants nobody
wrote down, notice when its own "race" resolved in program order, and chase
a surprising result as *suspicious* rather than report it as a win. A model
eager to please will produce a worse report from this exact skill than a
blunter one.

Empirical screen (cheap, behavioral): hand the model one Phase 4 seam
against a repo with a known answer. Pass requires it to force real
concurrency, not fake it with sequential awaits, and to catch a planted
harness bug. Exploit benchmarks measure the wrong thing for this workload.
Don't use them.

Effort allocation: Phase 1 is the fan-out stage, so use multiple agents or
a workflow there. Everything after it (Phases 2 to 4) is a single strong
model at max effort in the main context. Execution and falsification are
never downgraded to a cheaper delegate. If a Phase 4 seam absolutely must
be delegated, it still has to clear the two-signal screen above.

Model policy is a compatibility requirement, not a footnote. Authorized
good-faith security research is permitted usage on policy-gated frontier
models (Claude, GPT), but only when the engagement framing is present and
true. Without it, expect stalls mid-run. Refusal profiles differ by
provider and deployment, and open-weights models typically gate far less.
Pick a model and deployment whose usage policy covers offensive security
testing. If a step is gated, drop depth or switch to an enrolled
deployment. Never prompt around a refusal.

## Output discipline

Report in three sections. **Confirmed** carries inline PoC output, expected
against observed, and an honest severity: exploitable, latent, or
reliability-not-security. **Unproven** carries a real mechanism and what
was missing. **Held** carries the attacks that failed, with the specific
interleaving ruled out. End with: does anything change the usage
recommendation, and for whom. "Nothing exploitable found" IS a valid
deliverable when the attacks were real. Say what that confidence is worth.
