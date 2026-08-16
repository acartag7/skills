You are running the contract-first-product skill. The human asked
whether the locked tests pin "a note from the other room is
refused on every action that takes an id."

Facts:

- Open: the test creates a real note in room B (written by Bea)
  and Ada opens that id while naming room A. Expect refuse.
- Withdraw: the test uses that same note (Bea's) and Ada
  withdraws it while naming room A. Expect refuse.
- Replace: the test uses the literal id `guessed`. Expect refuse.
- Reply: the test uses a real id from room B as the reply
  pointer while creating in room A. Expect refuse.

Withdraw and replace are only allowed for the person who wrote
the note. Unknown ids are already tested elsewhere.

Is this freeze ready for those four actions? What is still
untested? Speak as you would to the human (people and rooms).
Do not write code.
