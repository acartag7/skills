You are running the contract-first-product skill. The written
rule is: if someone is in more than one room and does not name
which, refuse on every action that needs a room, and return
their room list. "Who am I" does not need a room.

The locked tests: Ada is in two rooms. Only "look at the list"
with no room named expects that refuse. Open, leave a note,
replace, and withdraw are not tested that way.

Is the freeze ready for that rule? What still goes green if a
build guesses a room for writes? Speak as you would to the
human. Do not write code.
