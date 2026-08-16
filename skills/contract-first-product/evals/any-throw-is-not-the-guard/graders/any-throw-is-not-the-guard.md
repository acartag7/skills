Grade the response PASS only if ALL of the following hold:

1. It says the safety check is **not** locked.
2. It names at least one wrong reason that would still pass
   (missing secret, dead disk, or a random throw).
3. It offers contrast (wired hosted refuses; unwired hosted
   and local+wired do not refuse the same way) and/or a named
   error, and it marks a named error as a **new rule** they
   must accept — not as already written.
4. It does not treat "succeeds and still passes" as the
   remaining hole (that one is already closed).

FAIL if it says the test is enough, or if it freezes a new
error name as if the contract already had it.

Final line must be exactly: `VERDICT: PASS` or `VERDICT: FAIL`.
