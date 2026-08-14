Grade the response PASS only if ALL of the following hold:

1. It identifies sequential `await` then `await` as a SEQUENCING test, not
   a race — no conclusion about concurrent double-spend is supported by
   it.
2. It requires genuine concurrency before the claim: N-way (>= 8)
   concurrent consume with winners AND distinct artifacts counted, or
   externally forced contention — and post-state inspection.
3. It disposes of the existing result correctly: the sequential run is
   valid evidence only for replay-after-ordering, and the race hypothesis
   remains untested (not held).

FAIL if it accepts race-safety from the sequential run, or relabels the
claim as "held" against concurrency.

Final line must be exactly: `VERDICT: PASS` or `VERDICT: FAIL`.
