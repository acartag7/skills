# Bringing Me a Decision (the disclosure format)

When something needs MY call — a security finding, a design tradeoff, a default
that's wrong, anything I have to weigh — write it the way I write a
vulnerability disclosure to another maintainer. Facts with `file:line` are the
appendix, not the report. I decide badly from a log of what was checked; I
decide well from consequence.

**Per item, in this order:**

1. **What it is** — one line of mechanism, plain words.
2. **What actually happens** — concrete, no jargon. Name the real actor and the
   real sequence. Not "the guard returns early" but "a request arrives and
   nothing counts it."
3. **If it's not fixed** — the consequence in MY world: what a user/attacker/
   operator ends up able to do, and who gets hurt. **This is the part that is
   always missing when I have to ask "so what?" — never skip it.**
4. **Where it already works** — the sibling that got it right. A defect reframed
   as an unmirrored fix is easier to judge and faster to fix ("you already
   solved this in X; Y just needs the same"). This repo's #1 defect class is
   exactly this shape.
5. **Recommendation with its reasoning attached** — "I'd fix this first
   *because* it's reachable through the flow the product markets, so the
   dangerous setup is the intended one." A severity label is not a reason.

**Then, across the set:** rank by what actually matters, not by CVSS-feel. Say
plainly what's exploitable-today vs latent vs reliability. Separate **mechanical
fixes** (nobody chose this; just wrong) from **design decisions** (the code does
what a comment says on purpose — changing it reverses a documented choice and
needs a contract update first). Never hand me both in one undifferentiated list.

**Always include what was DISPROVED.** The attacks that failed, the claim I
worried about that held. That's what makes the positives credible — same
function as the Held section in an adversarial assessment. A report with no
negative results reads like a search for things to say.

**Lead with what's genuinely good**, when it is. Not flattery — calibration. It
tells me where the bar already is and stops every report reading like the sky
is falling.

Worked example — same fact, two framings:

> ✗ `deployment-guard.ts:48` returns before the limiter check when
>   `dcr.mode !== "stateless"`.
>
> ✓ Your docs tell people to use stored DCR in production. That exact
>   configuration boots with no request budget on register, token, or approve —
>   so the recommended setup is the unthrottled one. If it's not fixed, one
>   script can create unlimited registrations against a production deployment
>   with nothing to notice or stop it. The guard already refuses the *stateless*
>   version of this; the stored path just isn't covered.

Same discovery. The second one I can act on.
