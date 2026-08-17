# ADR 0005 — Repo-local review eval from hosted rounds

Status: accepted (2026-08-16); amended 2026-08-16 (skill is
repo-local, not a marketplace plugin)

## Context

A public authorization library's hosted review history (130
pull requests) showed extra rounds clustering on one shape: a
CLEAN local pass closed the named instance, then the next head
was the same defect on the next adapter, leftover sentence,
stored row, or generated starter. Generic "sweep siblings"
prose was already in the house rules. It still signed CLEAN.

The method has to be written from **that** repo’s axes and
frozen heads. A marketplace plugin cannot name those cells
without either lying or leaking a product into this repo.

## Decision

1. The **working skill** lives in the originating product repo
   (project skill + matrices + evals + corpus). This marketplace
   does not install it.
2. This repo keeps the **method**:
   [repo-local-review-eval.md](../repo-local-review-eval.md).
   Other projects repeat the mine → name axes → write skill →
   freeze evals loop on their own history.
3. No product name, third-party target, or personal path in
   this repo.

| Gap | Landed in |
| --- | --- |
| Review closes `file:line`, not the behavior | playbook “What good is”; skill rules 2 and 5 |
| "Swept siblings" with no cells | playbook step 2; empty cell = FAIL |
| Leftover guarantee after a code fix | leftover-claim class |
| Wrap one call, claim every call | one-call-site class |
| Prepare-time policy, stored row skipped | stored-not-rechecked class |
| Guard exists, after the write | guard-after-open class |
| "Has unique name" is not shape | name-not-shape class |
| Library and example fixed, starter not | starter-not-library class |
| A review that always invents a blocker | class-closed control |
| Next hosted miss has nowhere to go | playbook step 4; freeze the same day |
| Want the same experiment on another repo | playbook steps 1–5, new matrices |
| Strong pass, ceremonial cost | playbook “Runner budget”; keep matrices |

## Consequences

Hosted review is supposed to verify, not discover the next
sibling. A follow-up PR whose whole job is the next cell of
the last merge is a skill miss in **that** repo, then a new
eval case there. This marketplace only tells you how to build
the next one.
