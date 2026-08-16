# How to talk to the human

The human decides badly from a log of checks. They decide well from
a story: who did what, what breaks, what to pick. If they have to
ask "be more clear," you used the wrong words.

This page is for **findings and choices**. The disclosure format
([disclosure.md](disclosure.md)) is the same bones. Here the rule
is: no shop talk.

## Banned in the mouth (say the second column)

| Do not say | Say |
| --- | --- |
| fixture / harness / seam | the test / the fake login the tests use |
| actor × state matrix | who can show up, and in what state |
| cross-resource / IDOR | they used an id from the other room |
| id-taking verb | open, replace, withdraw, reply |
| CSRF / Origin | a request from another website |
| codec / canonical base64 | the file bytes have to be real, not garbage |
| hashed / freeze gate | the locked tests (update the lock file too) |
| Batch N | this slice / the next slice |
| NotImplementedError | "not built yet" |
| requirePerson / no row | they signed in but we have no profile yet |
| residual | what we are choosing to live with |
| pin / lock (as a verb) | the test would fail if this were built wrong |

You may use the left column **inside a prompt** you hand to another
model. You may not use it when speaking to the human.

## One finding, then stop

Do not paste a review bot. Do not list ten holes. Walk one. Wait.
The next one after they say "ok" or "and".

## The only shape

1. **What it is** — one line, no file paths.
2. **What actually happens** — name the people. Name the rooms.
   Say the click or the tool call. If two rules can hide each
   other, say that in the story (see Hard cases).
3. **If we skip** — who gets hurt, in their world.
4. **The pick** — what each option does, in the same words.
5. **What you'd pick, and why** — one sentence. Not a severity
   badge.

Lead with what already works, when something does. Mention what
you checked that is **fine**, or the report reads like a hunt.

## Hard cases (these were expensive)

### A. One rule hides another

Some actions are only allowed for the person who wrote the note
(replace, withdraw). If Ada tries those on **Bea's** note, the
server can say "not found" because Ada is not the writer — and
never check the room. A test that uses Bea's note does not prove
the room check exists.

**Say it that way.** Then: Ada must write a note in the other
room herself, and try replace/withdraw on **that** id while
naming her own room. Now she is the writer. The only honest
refuse is "wrong room."

### B. "It threw" is not "it threw for the right reason"

A test that accepts any error except "not built yet" will pass
on a missing secret, a dead disk, or a random throw. The safety
check never has to run.

**Say:** we need the wired case to fail, and the unwired / local
cases to fail **differently** (or succeed). Or we name the exact
error — that is a new rule, ask first.

### C. The sentence and the table disagree

The list of routes has PATCH. The sentence says "POST and
DELETE need the website check." Someone will skip PATCH.
Another site can change retention.

**Say that.** The fix is one sentence, not a new route.

### D. The title lies

The test is named "works without email" and then signs in with
a verified email. The product must save that email. The test
never checks that email is missing.

**Say:** the test does not do what its name says. Build the
caller the name claims.

### E. One action of a family

The rule is: if Ada is in two rooms and does not name one,
every action that needs a room must refuse and list her rooms.
The test only checks "look at the list." Leave a note, open,
replace, withdraw still have no test. A build that guesses a
room for those still passes.

"Who am I" is not in this family — it does not need a room.

**Say that.** Then: same person, two rooms, no room named, on
**every** action that takes a room. Same class as "real id on
every action," not a new kind of hole.

## After a plan exists

Do not ask "do you want to do this in this session?" Do the
next step of **that** plan, or hand them the prompt that plan
named. Asking again is how work starts in the wrong tab.
