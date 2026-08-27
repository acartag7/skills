# Advisory-residual hunting

A finding generator, not a checklist. Use during Phase 1 (lead generation)
and before drafting any report (dedup pre-emption). Forged on a 2026 SDK
engagement where two confirmed findings came from exactly this loop, and a
third survived a duplicate close only because the exhibits below were
already in hand.

## The loop

1. **Enumerate the target's published security advisories** (GHSA pages,
   CVE records, the repo's security/advisories tab, release notes that say
   "security"). For each advisory touching your target's components:
2. **Read the fix DIFF, not the description.** Advisory prose compresses;
   diffs tell you exactly which lines the maintainers touched — and,
   critically, which adjacent lines they did not. Pull the real diff from
   the tags (`git diff <prev-fix-tag> <fix-tag> -- <file>`); archive it as
   evidence. If the advisory names a fix commit, don't trust the hash —
   squashes move; tags are the anchor.
3. **Sweep the unmirrored surface.** The fix defines a weakness class it
   addressed. Grep the same class across:
   - the sibling guards in the same file (a containment fix that never
     touched the delete guard; a modes fix in one module and not the other),
   - the sibling implementations (framework adapters, per-provider ports,
     the other package in the monorepo),
   - the same input reaching other commands/endpoints.
4. **Mine the target's own tests.** Grep the test suite for the input
   class. A test that asserts the dangerous input is *rejected*, sitting in
   the same block as a case that flows into an unvalidated path, is the
   strongest intent argument available: the maintainers threat-modelled
   this exact input, and one branch of it escaped. Quote the test block.

## What the loop hands you for free

- **The dedup exhibit.** "The credited fix changed exactly these two lines
  (diff attached); the guard this report defeats is a different check with
  one commit in its history (`git log -S '<guard string>'` → single
  introducing commit, never modified)" is close to unfalsifiable. Build
  this BEFORE writing the report; lead with it. A byte-identical diff and
  a single-commit pickaxe have survived every adversarial review thrown
  at them.
- **The calibration anchor.** The advisory's severity (cite the score on
  the page, labeled by version — advisories publish both v3 and v4) prices
  the class; your finding sits above or below it on stated impact legs.

## The trap: guards are not all boundaries

Before building severity on a defeated guard, **classify the guard**:

- **Boundary** — the code, its tests, and its docs treat it as adversarial
  defense (threat-model comments, adversarial test cases, security-release
  fixes touching it). Defeating it is a vulnerability.
- **Convenience** — error phrasing reads as accident-prevention ("cannot
  delete X itself"), tests cover happy paths, docs never mention misuse.
  Defeating it is a robustness bug. File it on the repo, not the bounty.

Probes: read the guard's error message as UX or security; check whether
security fixes ever touched it; check whether the docs promise anything.
And run the baseline-attacker test on **capability sets, not call counts**:
if the attack's precondition already grants the *capability* to reach the
same outcome — however many calls it takes — a faster spelling of that
outcome is not a finding, regardless of guard intent. ("One call instead
of N" is an efficiency delta; programs correctly read it as zero.)

Receipt (anonymized): a filed Medium — mechanism executed, seven bypass
spellings, dedup airtight — closed Informative because the defeated guard
was convenience and the attacker's precondition already conferred full CRUD
inside the defended directory. The close risk was named by three pre-filing
reviewers ("what's the delta over per-file delete?"); the answer ("one
call, plus aftermath") was true and insufficient. A capability-equivalence
check before filing would have routed it to a repo issue and saved the
submission.
