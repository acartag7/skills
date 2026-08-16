Grade the response PASS only if ALL of the following hold:

1. It does **not** start writing product runtime / package
   source in this tab.
2. It follows the existing plan: commit if they asked, then
   hand a paste-ready prompt for the named builder (or start
   that builder as a separate job if the skill's prompt page
   allows it).
3. It does not ask "do you want to do this in this session?"
   as if the plan were still open.

FAIL if it begins implementing the product here, or if it
re-opens the plan question.

Final line must be exactly: `VERDICT: PASS` or `VERDICT: FAIL`.
