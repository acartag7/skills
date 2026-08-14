# ADR 0003 — Disposition is always an ask

Status: accepted (2026-08-14)

## Context

Operator directive after the disclosure run: Phase 5 (close-the-loop) is
never automatic, and disclosure is not the default suggestion. The skill
regularly assesses third-party code; a standing nudge to disclose every
finding would push low-severity residue into maintainers' inboxes and turn
the operator's name into noise. Artifact disposition (the preserved PoC
workspace) is likewise the operator's call, not the skill's.

## Decision

- Phase 5 is always an ASK with two named branches: a PR with regression
  tests + invariant registry on repos the user owns; a private disclosure
  on third-party code.
- Disclosure is SUGGESTED only for findings that are plainly bad or
  dangerous (executed, high-consequence: credential theft, RCE-class,
  tenant data exposure). Otherwise the report is the deliverable and the
  disclosure decision stays with the user.
- Every assessment closes by asking what to do with the preserved
  artifacts: keep / promote / attach / discard — never unilateral disposal.

## Consequences

The skill never contacts a third party or pushes to a repo on its own
initiative; its terminal act is a well-formed question plus the evidence
needed to answer it.
