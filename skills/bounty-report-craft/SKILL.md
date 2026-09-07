---
name: bounty-report-craft
description: Turn a proven security finding into a filed bug-bounty report that survives adversarial triage — structural sentence, impact framing, dedup exhibit, minimal repro, submission hygiene. Use only after finding-triage returns a FILE verdict.
---

# Bounty Report Craft

Turn an executed security finding into a filed bug-bounty report that survives
adversarial triage. Applies AFTER the finding is proven (PoC executed, controls
fired) — this skill owns everything from framing to the submit button.

Forged on the Vercel Sandbox engagement (2026-08): report #3961933 went from
"technical SSRF description" to "filed High" through exactly this pipeline after
three adversarial review rounds caught a wrong mechanism sentence, an untraceable
status code, and a broken recommendation. Every stage below exists because
skipping it cost a round.

## The pipeline

### 1. Find the structural sentence

Before writing anything, answer: **can the operator deploy away from this?**
Map every defense in a table — for each: does it work, does it break the
feature, is it taught by the docs, is it price-gated?

```
| Defense                | State                                            |
| documented check       | enforced and useless here, because ...           |
| platform protection    | works but breaks the feature for the operator    |
| the one that works     | not taught, not in the example                   |
```

If no available configuration is both functional and protected, that is the
finding's spine: "the exposed class is every working deployment." Lead with it.
If defenses exist, the finding scopes to who lacks them — say that instead.

### 2. Execute the realistic variant, not just the documented one

Vendors' examples and operators' real deployments differ. Test both shapes:
the docs-verbatim configuration AND the one people actually deploy (the
credential-injecting proxy, not the bare forwarder). The realistic variant is
where impact lives. Label everything demonstrated vs scenario — a scenario
whose mechanics are all demonstrated is strong; claim it as scenario anyway.

### 2b. Enumerate the surface before claiming it exhausted

"Probed to its end" is only earned after an enumeration pass, or the report
claims exhaustion while having tested only the names you thought of. Before
writing "every X was probed":

- **Name sweep**: brute the procedure/endpoint namespace (a wordlist of
  plausible names; 200-vs-404 classification). A name you never guessed is a
  write you never tested.
- **Reflection**: try the introspection endpoints first (connect/gRPC
  reflection, OpenAPI /.well-known, GraphQL introspection) — one call can
  list the entire surface.
- **Port/path scan**: sweep the listener's siblings (other ports on the same
  CID/host, other services on the same socket family), not just the ports
  you already know.
- **Protocol ID**: a listener that RSTs HTTP and EOFs TLS still speaks
  SOMETHING — try the standard platform handshakes (PROXY protocol, HTTP/2
  preface, gRPC) before calling it inert.

If the sweep finds nothing, say the namespace was swept (N names, reflection
absent) — that converts an assumption into evidence. If it finds something,
the report just changed. Either way the sentence "every write was probed to
its end" is now one you can defend.

### 3. A/B every "X blocks it" claim

Any mitigation claim needs the single-variable test: same deployment, same
rig, flip only the defense. Off → attack works; on → attack blocked; nothing
else changed. Cross-deployment comparisons have a redeploy variable hiding in
them and reviewers will find it. If a defense flavor cannot be configured from
your accounts, say exactly that ("rejected with <error>, untested") — never
"untested" alone.

### 4. The adversarial review loop (non-negotiable)

The implementer never self-reviews. Build an isolated corpus (report + evidence
files ONLY, curate it — not the repo), secret-scan it (JWT shapes, token
prefixes, 43-char strings), then hand it to a clean-context reviewer with a
"report everything + confidence" mandate and six checks: claim-to-evidence
trace, mechanism correctness, overclaim scan, internal consistency, secret
leakage, severity reasoning. Fix everything in ONE pass per round. Repeat until
FILE-READY. Expect the loop to catch errors the loop itself introduced: one
round's fix put an unrecorded status code into the report and the next round
caught it. That is the system working. One writer per file — concurrent
editors clobber each other's fixes.

### 5. Package for the form

- Title: mechanism + boundary ("Cross-tenant X via unchecked Y in Z"). Name
  the root cause, not the consequence.
- CWE: pick the accurate leaf, not the umbrella (862 over 284 when the defect
  is a missing authorization check; SSRF is the effect, not the class).
- Custom fields: one coherent run — team, project, sandbox from the SAME
  evidence file, every ID verifiable in an attachment.
- Zip: neutral directory names, no internal numbering, rig sources included,
  every file the text references must be IN it. Secret-scan the zip, not just
  the repo copies.
- Description follows the program's template exactly. Impact section written
  in scenario register: "your server answers strangers with your own
  credential," not "the threat actor exfiltrates via SSRF vector." One
  plain-terms sentence at the end of the summary for the non-expert reader.
- Writing passes last: unslop + technical-writing. No em dashes, no
  semicolons where periods work, passive flipped to name the actor.

**Capture discipline, mechanically.** Never hand-paste output blocks:
generate them FROM the capture file, and before filing, `diff` every quoted
block against a fresh run of its script. Three drift incidents from one
engagement's pre-flight: a padding difference in a hand-pasted block, a
fixed mock port that collided (EADDRINUSE on the reviewer's machine — pick
ports dynamically), and an ephemeral port inside a block labeled "verbatim."
Label nondeterministic interleavings as such. A statistic without its
attached script reads as fabricated even when it is real — ship the script
with its exact parameters or drop the number.

**Port to the published artifact before filing.** Assessment PoCs that
import the local checkout are evidence, not submissions. Port every PoC to
package imports pinned to the exact published version, clean-install from
the lockfile, re-run, re-capture. Every port surfaces something: wrong
export names, fixed ports, harness assumptions. A triager who installs the
package and runs your script cold must succeed first try.

### 6. Anticipate the triage anchor

Before filing, grep the program policy for ANY pre-existing wording that
sounds like your finding ("ForwardURL misroute", "sandbox escape"). Triage
anchors on pre-categorized rows. Prepare the distinction as a paste-ready
comment NOW, save it in memory, and use it the moment the anchor appears:
"Misroute moves traffic; this mints the key that unlocks it."

**Classify every guard you defeat: boundary or convenience.** Read the
guard's error message as UX or as security; check whether security fixes
ever touched it; check whether docs promise anything. Then run the
baseline-attacker test on CAPABILITY SETS, not call counts: if the attack's
precondition already grants the capability to reach the same outcome —
however many calls it takes — a faster spelling of that outcome is not a
finding, guard or no guard. Receipt (anonymized): a filed Medium, mechanism
executed and undisputed, closed Informative in under a day because the
defeated guard was accident-prevention and the attacker's precondition
already conferred full CRUD inside the defended directory. The close risk
was named by all three pre-filing reviewers ("what's the delta over doing
it file-by-file?"); the answer — "one call, plus a broken-until-restart
aftermath" — was true and weighed as zero. A convenience guard defeated is
a repo issue, not a submission. When the closure lands anyway, mine the
triager's own boundary definition — their words price your remaining
findings (one closure's "the boundary this helper defends is containment"
became the frame for the two findings that crossed it).

## Venue triage (before the form exists)

**Gate zero: nothing enters this skill without a finding-triage verdict.**
Run the input-origin × boundary classification first (the
`finding-triage` skill); config-origin and undefended-boundary findings
are repo issues by venue and never reach this pipeline.

- **Availability-only impact → the repo's issue tracker**, however clean
  the mechanics. Most programs exclude DoS outright; three independent
  reviewers converging on that reading is the signal to stop drafting.
- **Convenience-guard defeats and local-robustness findings → repo issue**,
  framed as correctness/robustness with the repro attached.
- **A refuted impact leg withdraws the REPORT**, not just the paragraph —
  filing the surviving half of a half-refuted report is the automated-
  submission profile programs close on sight.
- **One report first, then stagger.** Never same-day batches of
  same-skeleton reports; the AI-authorship clause is exercised over a
  submitter, not a report, and a weak sibling sinks a strong one.
- **Beta-window findings you can wait out get waited out** — dated by git
  (introducing commit + first containing tag), never by version-string
  identifiers. Filing inside the window buys a lower tier and a dent.

## Severity discipline

- The 25% inflation penalty is real money. Claim the tier whose effect you
  DEMONSTRATED, state the concession that keeps you out of the higher tier,
  and hand the upgrade decision to triage in writing. A reviewer suggesting
  Critical is not your claim to adopt.
- Never file a number that traces to no file. If the stdout wasn't saved, the
  run didn't happen — re-run it or write "not preserved."

## Output

Filing-ready: title, CWE, class, boundary, custom fields, description block,
impact block, sanitized zip, and a memory note with the triage-anchor counter.
The repo keeps the full evidence record; the submission text is derived from
it, never the other way around.

## Filing-channel verification

Structured-scope pulls, the named-asset vs umbrella-clause trap, bounty-table ceilings, advisory-ID verification, and the product-reachability lane: see `references/filing-channels.md`.
