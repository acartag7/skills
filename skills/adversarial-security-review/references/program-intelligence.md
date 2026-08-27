# Program intelligence (bounty-program scoping before you draft)

For HackerOne-hosted programs. Do this BEFORE writing any report — it
decides what is fileable, where, and at what tier. Forged on a 2026
engagement where a live scope pull changed the filing plan twice.

## Pull the program data via GraphQL (the page is useless)

Program pages are JS shells; a plain fetch returns nothing. The public
GraphQL endpoint works unauthenticated:

```
curl -s https://hackerone.com/graphql -X POST \
  -H 'Content-Type: application/json' \
  -H 'Origin: https://hackerone.com' \
  -d '{"query":"query($h:String!){team(handle:$h){id handle name policy
      structured_scopes(first:50){edges{node{asset_identifier asset_type
      eligible_for_bounty eligible_for_submission instruction max_severity}}}}}",
      "variables":{"h":"<handle>"}}'
```

Field errors are self-describing — when you guess wrong, the error names
the right field (`eligible_for_bounty`, argument `handle`). Archive the raw
JSON as evidence. You get: every asset row with Core/Non-Core tier,
bounty/submission eligibility, max severity, and the full policy text.

## What to check, in order

1. **Scope-row ambiguity.** The same repo can sit under multiple rows at
   different tiers — an org-wide `SOURCE_CODE` row marked Non-Core *and* a
   product-specific row marked Core that links the exact repo. Name the
   asset row you are filing under, in the report header, quoting its
   linked asset. Never assume the mapper picks the generous row.
2. **Exclusions vs your finding's impact class.** Map each finding's
   impact to the policy's exclusion list before drafting. Common kill:
   "denial of service attacks" excludes availability-only findings no
   matter how clean the mechanics — reliability findings go to the repo's
   issue tracker, not the bounty. No published bounty matrix → describe
   impact and cite credited advisories as calibration; claim no dollar
   tiers you cannot quote verbatim.
3. **Beta / Research Preview windows, dated by git — never by names.**
   Programs commonly treat beta features as non-core for ~1 month after
   release. NEVER infer a ship date from a version-string identifier
   (`something_20260401` is an identifier, not a date — reading it as one
   produced a factually false scoping claim that nearly sank a report).
   Date the vulnerable FILE: introducing commit
   (`git log --follow --diff-filter=A -- <file>`) plus first containing
   tag (`git tag --contains <sha> | sort -V | head -1`). If the finding is
   inside a window you can wait out, HOLD it: filing early buys a lower
   tier and a credibility dent for nothing.
4. **Provenance discipline for advisory facts.** Cite CVE↔GHSA↔CWE↔CVSS
   only from primary sources (the GHSA page, NVD). Search-result summaries
   and aggregator pages produce phantom mappings (a "conflicting CWE"
   that exists nowhere primary). Advisories dual-map CWEs and dual-score
   CVSS (v3 and v4 both printed) — cite the one on the page, labeled.

## Filing-lifecycle rules (earned the hard way)

- **One report first, then stagger.** Same-day, same-skeleton batches are
  the profile triage associates with automated submissions; several
  programs close "AI-written" reports at discretion. The clause is
  exercised over a *submitter*, not a report — a weak sibling can sink a
  strong one.
- **Chains belong in one report** when the policy permits chaining to
  demonstrate impact (escape → write → exfil is one vulnerability whose
  legs are designed behavior on a poisoned input, not three findings).
- **Track the program's SLA** (commonly: acknowledge within 3 business
  days) and the beta windows you are holding findings for.
- **On an Informative close**: if the rationale is a stated threat-model
  position (not a factual dispute), do not appeal — extract the boundary
  definition from the triager's own words; it prices your remaining
  findings. One engagement's closure language ("the boundary this helper
  defends is containment") became the frame for the two findings that
  *did* cross it.
