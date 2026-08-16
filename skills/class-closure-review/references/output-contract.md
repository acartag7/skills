# Output contract

Emit every block. Silence is not PASS.

```text
REVIEWED_SHA: <full exact head>
BASE: <full base or explicit uncommitted>
VERDICT: PASS | FAIL | STOP

MATRICES:
- M1: <hit|n/a|clean per occupied cell, or n/a — reason>
- M2: …
- M3: …
- M4: …
- M5: …
- M6: …
- M7: …

CLAIM TRACE:
- "<guarantee sentence>" → <file:line> + <test that goes red if removed>
  (or: leftover / unbacked — FAIL)

CLASS CLOSURE:
- Behavior: <one sentence>
- Cells checked: <list>
- Still open: <list or none>

FINDINGS:
- [P1|P2|P3] <file:line> — <title> — <empty cell + impact + smallest fix>

CLEAN:
- <named areas traced and clean>

CONFIDENCE: high | medium | low — <reason>
```

## Verdicts

- **PASS** — every applicable cell is `hit` or `clean`, every new
  guarantee traces to code and a biting test, no P1/P2. P3 may
  ship only when named and left for the owner.
- **FAIL** — any empty applicable cell, leftover claim, guard
  after a side effect, test that would stay green, or unwrapped
  sibling of a claimed closed boundary.
- **STOP** — a **new** edge class the contract never named. Do
  not start another review round. The owner amends the contract
  or re-cuts the slice. This is the only time review is allowed
  to discover instead of verify.

P1 and P2 block. Do not offer a silence-window merge. Do not
treat "the previous local reviewer said CLEAN" as a cell.

## After a hosted finding

The next local pass is not "did we change the reported line?"
It is: name the behavior, fill the matrix that finding belongs
to, close every occupied cell, then re-review **this** SHA.
A follow-up PR whose whole job is the next sibling of the last
merge means this skill was not used.
