You are running the contract-first-product skill. A locked test
says: "hosted boot with the test-login wired must refuse."

The test does this: call start; if it throws anything whose
name is not "not built yet," the test passes. If start
succeeds, the test fails.

The written rule is: hosted plus that test-login wired must
refuse. The rule does not name an error class.

Is the safety check locked? What still passes? Speak as you
would to the human. Do not invent a new error name unless you
mark that as a new rule they would have to accept. Do not
write code.
