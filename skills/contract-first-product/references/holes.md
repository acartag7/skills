# Holes a freeze still lets through

A later review found these after the docs were already "frozen."
Each time, either the test did not build the case it named, or
two sentences in the docs disagreed.

Before freeze: for each item, point at a test (or a written
cell) that a **wrong** build would fail. If you cannot, you are
not done.

Walk these in [talk.md](talk.md) language when the human is in
the room. This page is the checklist for you.

## 1. The title lies

The name says "without email" or "from the other room." The
test signs in with a verified email, or uses the fake id
`"guessed"`.

A wrong build still passes: it always stores email; it only
rejects ids that do not exist.

**Fix:** build the person the title names. Check the exact
shape (email missing, not merely "id hidden").

## 2. Fake id, not a real one from the other room

The test uses `"from-other-room"` and never creates a note
there. That is "unknown id," already tested.

A wrong build still passes: it looks up by id only and skips
the room.

**Fix:** create a real note in room B. Call the action with
that id and room A. Do this for **every** action that takes an
id (open, replace, withdraw, reply), not the first one you
thought of.

**Also (hard case A):** replace and withdraw are only for the
writer. If Ada uses **Bea's** note, "not the writer" fires
first and a missing room check never runs. Ada must write the
note in B herself.

## 3. The test catches its own failure

The test says "fail if boot succeeds," then catches that
failure and treats it as a pass.

**Fix:** do not catch the test's own "this should have failed."

## 4. Any error counts as the safety check

After (3), the test still accepts every error except "not
built yet." A missing secret passes. The seed check never
runs.

**Fix:** hard case B. Contrast boots, or name the exact error
(that name is a new rule — ask).

## 5. Every test is the same person

Every test uses the same login. The product allows one created
room. The second test dies on quota — or only works because
the tests secretly do not share a store.

**Fix:** new person, new email, every time. Sharing a store
across tests is not a lock.

## 6. The sentence and the table disagree

The route list has PATCH. The sentence says POST/DELETE.
Someone skips the other-website check on PATCH.

**Fix:** hard case C. One sentence. Every non-GET, or list
every method that exists.

## 7. "Who am I" needs a profile we have not written

A signed-in person with no rooms and no saved email must
still be able to ask who they are. The code demands a
profile row and refuses.

**Fix:** write that cell. Empty list, no email. Do not demand
a row onboarding has not created.

## 8. The test login is thinner than the contract

The contract stores which client wrote the note (website vs
their agent, protocol, display name). The test login is only
"this person." Every note is recorded as the website.

**Fix:** the test login must be able to say website vs agent,
and carry what the contract stores.

## 9. Garbage is stored as if it were real

Bad file bytes are accepted. An invite email with a tab or a
newline is stored, cannot match a real login, and still uses
up a slot.

**Fix:** refuse. Do not store. After trim, an invite email
with any blank character is refuse.

## 10. You decided for them

A number, a leftover risk, or a "never" appears in the docs.
They did not pick it. Later review treats it as law.

**Fix:** ask, or it stays in open questions. Do not write
"they accept this leftover" unless they said so.

## 11. One action of a family

The rule is: two rooms and no room named → refuse on every
action that needs a room. The test only covers "look at the
list." Leave / open / replace / withdraw are untested.

A wrong build still passes: it refuses the list, then silently
picks a room for writes (or for open).

**Fix:** hard case E. Same person, two rooms, omit the room,
on **every** action that takes a room. "Who am I" is not in
the set. Same sibling class as hole 2 (every action, not one).
