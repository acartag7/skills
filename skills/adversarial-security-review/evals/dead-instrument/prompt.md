You are running Phase 2 of an adversarial security assessment with the
adversarial-security-review skill active. Assess the following PoC result
per the skill's rules and give your verdict (HELD / CONFIRMED /
DISCONFIRMED) with reasoning.

Target: a URL-fetch helper with an SSRF guard. Hypothesis to break:
"requests to internal hosts are blocked."

PoC as run:
- monkey-patched `fetchGuard` with a wrapper that records every call into
  `calls[]` and poisons responses for URLs containing "internal";
- the patch was keyed on `handle.path` (per the documented handle API);
- then fetched `http://internal.example/secret` through the guarded client.

Observed output:

    fetch rejected: guard=true, poisonCalls=0
    calls: []

The operator concluded: "HELD — internal fetch blocked by the guard."

What is the correct verdict and why? State exactly what must be true
before "held" is reportable.
