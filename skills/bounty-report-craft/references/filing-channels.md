# Filing-channel and scope verification (before you write the report)

Where a finding is FILED is a factual question with a machine-readable answer.
Verified on HackerOne live (2026-08-26); every rule below redirected or killed
a filing plan that looked right on paper.

## HackerOne structured scopes via public GraphQL

Program pages are SPAs (no content server-side), but
`POST https://hackerone.com/graphql` answers unauthenticated:

```
curl -s -X POST https://hackerone.com/graphql -H 'Content-Type: application/json' \
  -d '{"query":"query { team(handle: \"<handle>\") { name state structured_scopes(first: 100) { edges { node { asset_identifier asset_type eligible_for_bounty max_severity instruction } } } } }"}'
```

- `__schema` introspection is blocked; `__type(name:"StructuredScope")` field
  introspection works — use it to discover fields (`bounty_table_rows` carries
  per-tier amounts: `critical/high/medium/low` + `*_minimum` + `use_range`).
- The handle is often NOT the company name (`vercel-open-source`, not
  `vercel`). Probe candidates in a loop; `data.team: null` = no such handle.
- `state: null` teams exist (gated/legacy) — absence of scopes is not absence
  of program.

## The three traps

1. **Named asset ≠ umbrella clause.** Read `instruction` text on EVERY
   matching row. Live example: `github.com/anthropics` is bounty-eligible on
   Anthropic's program, but the `github.com/modelcontextprotocol` row on the
   same program says `eligible_for_bounty: false` with routing text ("report
   to MCP maintainers via the repo Security page") AND a second lane in the
   same string ("implementations of MCP in Anthropic products … submitted
   under the affected asset"). One GraphQL pull replaced an entire
   filing-strategy assumption — and killed a "$15k/finding" figure that the
   live bounty table capped at $10k Core / $5k Non-Core.
2. **Bounty tables over marketing numbers.** Pull `bounty_table_rows` and use
   the ceilings; headlines and blog posts inflate.
3. **Deconfliction citations must be verified IDs.** A report's deconfliction
   paragraph citing a wrong GHSA/CVE ID is where triage stops trusting. Verify
   every advisory ID against NVD/GitHub advisory DB before it appears in a
   report (live catch: pack cited GHSA-cqwc-fm46-7fff for CVE-2026-0621; the
   real advisory is GHSA-8r9q-7v3j-jr4g).

## Product-reachability conversion lane

"Bundled dependency" findings convert to product-asset bounty reports only
with reachability proof: string-grep the product binary for the dep
(cheap but insufficient — scaffold-template strings look like vendored deps),
then run a behavioral probe (hostile local server against the live product).
Gate on the owner's go-ahead when it touches their live config.
