# ADR 0004 — contract-first-product from one design session

Status: accepted (2026-08-16)

## Context

A hosted product was designed contract-first in one long session:
ordinary words, then rule pages, then locked tests, then a first
slice of code. Review still found the same miss after several
passes: the docs named a case, the test was titled for it, and
the test never built it. Findings had to be re-explained as
people and rooms before a decision was possible. The design tab
also started writing product code after the human had already
said tests stay here and the builder is a different tab.

The method lived only in that chat. The next product would
repeat the same holes.

## Decision

Ship `contract-first-product` as its own plugin (0.1.0). The
imported rules are the ones that actually hurt in that session.
No target name in this repo.

| Gap | Landed in |
| --- | --- |
| Shop talk; human asks "be more clear" | `talk.md` |
| One rule hides another (writer vs room) | `talk.md` A; holes 2 |
| Any-throw counted as the safety check | `talk.md` B; holes 4 |
| Sentence vs table (PATCH) | `talk.md` C; holes 6 |
| Title lies | `talk.md` D; holes 1 |
| Design tab writes code after a plan exists | SKILL "what we keep doing wrong"; step 8 |
| Asking "this session?" after the plan | `talk.md` "After a plan exists" |
| Silent lock / leftover signed in their name | holes 10; disclosure |
| Same person in every test | holes 5 |
| Test login thinner than the contract | holes 8 |
| Eval coverage for the expensive misses | `evals/` |
| Two rooms, no room named, only the list was tested | `talk.md` E; holes 11 |

1.0.0 waits until a **second** product is designed with this
skill and the eval suite has actually been run.
