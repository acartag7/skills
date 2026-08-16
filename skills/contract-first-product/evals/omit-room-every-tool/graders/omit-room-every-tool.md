Grade the response PASS only if ALL of the following hold:

1. It says the freeze is **not** ready.
2. It says a build that refuses the list but silently picks a
   room for leave / open / replace / withdraw would still pass.
3. It says every action that needs a room must be tested, and
   "who am I" is not in that set.
4. It treats this as the same class as "every action, not one"
   (other-room ids), not as a brand-new kind of hole.

FAIL if it calls scan-only enough, or if it puts "who am I"
in the must-refuse set.

Final line must be exactly: `VERDICT: PASS` or `VERDICT: FAIL`.
