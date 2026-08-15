# Novelty floor — hunting below the taxonomy (Level 3)

Generation and selection are different jobs, and only one of them is
bounded by the corpus. A bug class is never a new primitive — it is a new
COMPOSITION (this primitive + that assumption + this sequence), and the
corpus contains scattered findings, not the combination space: it pins
the ingredients, not the recipes. So the model does not have to invent
from nothing. It has to NOTICE.

The operating principle: **the world invents; the model reduces.** Route
hypothesis formation through ground the priors don't contain — the
dependency's actual source at this pinned version, a live differ where
the system under test generates the observation, fuzz anomalies from the
world — then explain and reduce what disagrees. Testing never constrains
generation; it decides what may be CLAIMED. The model was tested against
the map; the bug lives in the territory.

This mode runs at Level 3 alongside/after Phase 4, with its own reserved
budget slice, and it is ALLOWED to produce nothing — that is honest
depth, not failure. Every mechanism here feeds the invariant engine
(Phase 3), it does not bypass it: the composition hypothesis is still
"assumed here, established nowhere."

## 1. Read the primitive, not the app

Enumerate the layers under the target's own code: the URL parser, the
router, serializers/deserializers, codecs, normalizers, regex
construction, and the dependency's ACTUAL source (vendored,
node_modules, site-packages — read it, don't imagine it). For each
boundary crossing, write two lines: the contract the caller assumes, the
behavior the callee actually implements. An assumption without a
promised contract is a lead. This is where smuggling-class bugs live:
nobody finds them in the app; they find them in the proxy's header
parser.

## 2. Differential pairs

List every input two components both interpret: URL canonicalizer vs
fetcher; path normalizer vs the OS; JSON body vs form decoder; Unicode
NFC/NFD and case folding; percent-decode order; adapter A vs adapter B
implementing the same interface. Build a tiny differ: fire the SAME
crafted payload at both, diff the outputs. Disagreement between two
interpreters of one input is the raw material of every parser-differential
class.

## 3. Spec ambiguity mining

For each spec the code claims to implement (RFC, protocol, even a
README's promise), list where the spec is silent, ambiguous, or a MAY.
Two components choosing differently on a MAY is a vulnerability-class
candidate: write both readings, then construct the input where
reading-A validates and reading-B executes.

## 4. Primitive properties (cheap, systematic)

- **Idempotence**: `normalize(normalize(x)) == normalize(x)`?
  Non-idempotent normalizers breed double-decode classes.
- **Canonical uniqueness**: can two encodings of one resource both be
  accepted? → cache-key confusion, authz-bypass classes.
- **Survival under transform**: validate-then-transform-then-use — does
  the validated property survive the transform?
- **Boundary bytes**: empty input, doubled delimiter, the off-by-one
  length — where does interpretation flip?

## 5. Anomaly-first: fuzz to NOTICE, not to prove

Small directed fuzzers at the chosen boundary (use the §2 differ).
Treat any anomaly — surprising output, disagreement, crash, latency
spike — as a hypothesis to EXPLAIN and REDUCE, never to report. Most
anomalies are harness lies (see the gallery); the reduction step is
where novelty is decided.

## 6. Distant-domain transplant

The one move where the corpus prior HELPS: it is prior for another
ecosystem, therefore novel for this stack. Ask what bug class exists in
the analogous layer elsewhere and what its shape is here — header
smuggling → multipart/MIME boundaries; SQL quoting → GraphQL/SPARQL
identifier positions; path traversal → host/URI normalization;
prototype pollution → config-object merging; SSRF pinning → keyring/
secret-path resolution. Normal rules still apply: no PoC, no finding.

## Output

A composition-hypothesis list, each entry: the layers/pair involved,
assumed-vs-actual contract, the constructed disagreement input, and the
reduction status. Anything executed-and-irreducible reports through the
normal lanes (Confirmed/Held/Disconfirmed). If a mechanism generalizes
past this codebase — same shape would break other implementations of
the same spec — SAY SO in the finding; that is the Tier-3 signal, and
it changes the disclosure posture (treat it as a class, coordinate
before publishing the shape).

## Honesty rules

- Novelty claims get no discount: executed, reduced, or downgraded.
- Reserved budget slice (suggest ~20% of the Level-3 budget). Spending
  the slice with no confirmed hypothesis is a valid outcome — record
  what was read and what was ruled out, same as Held.
