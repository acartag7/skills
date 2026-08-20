# Novelty floor: hunting below the taxonomy (Level 3)

Generation and selection are different jobs, and only one of them is
bounded by the corpus. A bug class is never a new primitive. It is a new
COMPOSITION: this mechanism, that assumption, this sequence. The corpus
contains scattered findings, not the combination space. It pins the
ingredients, not the recipes. So the model does not have to invent from
nothing. It has to NOTICE.

The operating principle: **the world invents; the model reduces.** Route
hypothesis formation through ground the priors don't contain. Read the
dependency's actual source at the pinned version. Run a live differ where
the system under test generates the observation. Fuzz anomalies out of
the world. Then explain and reduce what disagrees. Testing never
constrains generation. It decides what may be CLAIMED. The model was
tested against the map; the bug lives in the territory.

In plain terms: a model only knows what it has read, so its first ideas
are always old bug types. New bugs are new combinations of old pieces,
and this exact program was never in anything the model read. So this
mode's job is not creativity. It is experiments that produce surprises,
meaning two parts disagreeing or weird outputs, and then explaining them.
The running program invents; the model explains.

This mode runs at Level 3, alongside or after Phase 4, with its own
reserved budget slice. It is ALLOWED to produce nothing. That is honest
depth, not failure. Every mechanism here feeds the invariant engine in
Phase 3; it does not bypass it. The composition hypothesis is still
"assumed here, established nowhere."

## 1. Read the primitive, not the app

Enumerate the layers under the target's own code: the URL parser, the
router, serializers and deserializers, codecs, normalizers, regex
construction, and the dependency's ACTUAL source, whether vendored, in
node_modules, or in site-packages. Read it, don't imagine it. For each
boundary crossing, write two lines: the contract the caller assumes, and
the behavior the callee actually implements. An assumption without a
promised contract is a lead. This is where smuggling-class bugs live.
Nobody finds them in the app. They find them in the proxy's header
parser.

## 2. Differential pairs

List every input two components both interpret: the URL canonicalizer and
the fetcher, the path normalizer and the OS, a JSON body and a form
decoder, Unicode NFC against NFD, case folding, percent-decode order, and
any two adapters implementing the same interface. Build a tiny differ:
fire the SAME crafted payload at both and diff the outputs. Disagreement
between two interpreters of one input is the raw material of every
parser-differential class.

## 3. Spec ambiguity mining

For each spec the code claims to implement, whether an RFC, a protocol,
or a README's promise, list where the spec is silent, ambiguous, or a
MAY. Two components choosing differently on a MAY is a
vulnerability-class candidate. Write both readings, then construct the
input where reading A validates and reading B executes.

## 4. Primitive properties (cheap, systematic)

- **Idempotence.** Does `normalize(normalize(x)) == normalize(x)`?
  Normalizers that aren't idempotent breed double-decode classes.
- **Canonical uniqueness.** Can two encodings of one resource both be
  accepted? That is cache-key confusion and authz-bypass territory.
- **Survival under transform.** In a validate, then transform, then use
  pipeline, does the validated property survive the transform?
- **Boundary bytes.** Empty input, a doubled delimiter, the off-by-one
  length. Where does interpretation flip?

## 5. Anomaly-first: fuzz to NOTICE, not to prove

Run small directed fuzzers at the chosen boundary, using the differ from
section 2. Treat any anomaly, whether surprising output, disagreement,
crash, or latency spike, as a hypothesis to EXPLAIN and REDUCE, never to
report. Most anomalies are harness lies; see the gallery in the harness
playbook. The reduction step is where novelty is decided.

**Anomalies that contradict your own prior negatives outrank anomalies
that contradict your expectations.** An unexpected success on a surface
you previously closed, a probe that should have failed but succeeded, a
"denied" operation quietly working under different parameters: each
means one of your instruments lied, so every conclusion built on that
instrument is now unknown, and the re-test redraws more of the map than
any new finding would. Field instance: an attack surface was "closed" by
three stacked probe artifacts; a throwaway step with a different access
width succeeded accidentally, and re-testing with that width opened the
surface the whole assessment had been built around. Chase
result-versus-prior-book discrepancies before result-versus-hypothesis
ones.

## 6. Distant-domain transplant

The one move where the corpus prior HELPS: it is prior for another
ecosystem, and therefore novel for this stack. Ask what bug class exists
in the analogous layer elsewhere and what its shape is here. Header
smuggling maps to multipart and MIME boundaries. SQL quoting maps to
GraphQL and SPARQL identifier positions. Path traversal maps to host and
URI normalization. Prototype pollution maps to config-object merging.
SSRF pinning maps to keyring and secret-path resolution. Normal rules
still apply: no PoC, no finding.

## Output

Produce a composition-hypothesis list. Each entry names the layers or
pair involved, the assumed-versus-actual contract, the constructed
disagreement input, and the reduction status. Anything
executed-and-irreducible reports through the normal lanes: Confirmed,
Held, or Disconfirmed. If a mechanism generalizes past this codebase,
meaning the same shape would break other implementations of the same
spec, SAY SO in the finding. That is the class-level signal, and it
changes the disclosure posture: treat it as a class and coordinate before
publishing the shape.

## Honesty rules

- Novelty claims get no discount. They are executed, reduced, or
  downgraded like anything else.
- Keep the reserved budget slice, suggested at about 20% of the Level-3
  budget. Spending the slice with no confirmed hypothesis is a valid
  outcome. Record what was read and what was ruled out, same as Held.
