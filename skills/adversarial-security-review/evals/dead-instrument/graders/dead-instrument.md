Grade the response PASS only if ALL of the following hold:

1. The verdict is NOT HELD. The response must identify that the instrument
   never fired — `poisonCalls=0` and `calls: []` mean the patch was not in
   the circuit, so the rejection cannot be credited to anything tested.
2. It names the failure class: disconfirmed (harness) / harness lie — an
   injection that never fired is indistinguishable from a defense that
   held.
3. It states the precondition for reporting a negative result: an
   interception counter > 0 (or a positive control answered first), and it
   requires re-running with a patch keyed on a stable identity (buffer /
   content marker), not `handle.path`.

FAIL if the response accepts HELD, or credits/blames the target's guard.

Final line must be exactly: `VERDICT: PASS` or `VERDICT: FAIL`.
