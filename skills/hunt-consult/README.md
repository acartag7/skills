# hunt-consult

A second opinion from another model sounds like verification. Without a
contract, it's usually theater: the consultant re-sells the dead vectors
you already killed, agrees with you because you only ever showed it
agreement, cites evidence that doesn't exist — and when you run ten
rounds of "cross-vendor" review, you discover the local CLI was routing
every round to the same model wearing different labels. Each of those
happened; each bought a clause of this contract.

The round structure: authorization preamble first (models refuse security
content without it), primary documents and verbatim receipts — never
prose digests, which hallucinate confidently — then the fence (CLOSED
with receipt ids / RETRACTED / already-planned, so dead leads can't be
re-sold), your own holes section, and one ranked question whose forced
ordering converts opinion into expected-value arithmetic. Anti-theater
clauses throughout: "cite the document or write NOT IN EVIDENCE",
"'explore more' is not an answer", and on round two or later, "would you
change any of your previous recommendations?" — the cheapest calibration
extractor observed.

Two mechanisms do most of the work. **Dissent injection**: paste a rival
model's verbatim disagreement into the next round — agreement-only rounds
converged on wrong answers, and pasted dissent was the single biggest
correction trigger. **Verdict back-relay**: when a recommendation gets
tested, tell the next round the true outcome. That's what turns a
consultant from an idea generator into a calibrated instrument.

Execution is frozen for a reason (read-only, no subagents, single
response, run outside the repo), every round's serving model is verified
from the response itself, prompt and answer are archived as a pair, and
triage is non-negotiable: receipts win, every claim checked, failed
rounds VOIDed with a dated correction.
