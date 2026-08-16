# Matrices

Fill every applicable cell. `n/a` needs a one-line reason that
cites the tree (the path does not exist), not the ticket.

Mark `hit` when the new behavior applies there and you traced it.
Mark `clean` when you traced it and the behavior already matches.
An unmentioned cell that exists in the tree is empty → FAIL.

## M1 — Path and composition root

**When:** a guard, parser, budget, error wrapper, or claim about
"every request" / "no port error reaches the client."

| | Library factory | Example app | Generated starter | Scaffold / init |
|---|---|---|---|---|
| Adapter A | | | | |
| Adapter B | | | | |
| Adapter C | | | | |
| Secondary surface (pairing, callback, admin) | | | | |
| Throw | | | | |
| `{ ok: false }` / result object | | | | |
| Getter / Proxy / capability read | | | | |

A wrap that only covers `find` while `create` / CAS / getters still
throw the inner error is one filled cell, not a closed class.

## M2 — Persistence and policy revalidation

**When:** allowlists, matchers, schema, store identity, clocks,
or any policy that already-issued state must obey.

| | New entry | Already-stored row | In-flight token / pending grant | Replica / copy | Case-variant name |
|---|---|---|---|---|---|
| Store A | | | | | |
| Store B | | | | | |
| Store C | | | | | |
| Inbound FK / referring object | | | | | |
| Outbound FK / referenced object | | | | | |

A prepare-time matcher that approve() or an old token does not
re-run is an empty stored / in-flight cell.

## M3 — Leftover claim and status number

**When:** any guarantee changed, or a phase / count / "current
status" line moved.

Grep `never|always|cannot|enforced|rejected|only|must` on:

- the file you edited
- sibling contract pages (the numbered set, not just one section)
- threat residuals and field annotations / JSDoc
- verification receipts and status headlines (counts must be
  recounted, not left from the previous row)
- operator guides that still teach the old rule

One leftover "always 200" next to a new generic 500 is FAIL.

## M4 — Guard before every side effect

**When:** a new boot, bind, ack, or reject-at-start guard.

The guard must run **before**:

- secret / key file create
- store open or migrate / DDL
- outbound discovery or other network
- listen / bind
- success audit
- single-use consume
- durable write

"The guard exists" is not enough if it sits after `openStore`.

## M5 — Wire form and snapshot-once

**When:** redact, compare, or parse untrusted strings; or config
is read from accessors.

Cells: raw string; `+` form; percent-encoded form; header bag vs
map; case-duplicate keys; Unicode whitespace; value at
construction vs value after the transport mutates it.

Read accessors **once** and reuse the snapshot. A getter that
returns the safe object here and the unsafe object later is FAIL.

Unexpected results fail closed: invalid enum must not become the
permissive default; a non-callable hook must not be treated as
"limiter down, allow."

## M6 — Schema exact-shape

**When:** admitting a database, lockfile, or "has unique X" check.

"Has a unique column named `jti`" is not shape. Also reject
(or explicitly allow, in the contract):

- prefix / partial keys
- functional / expression keys
- competing uniques on another column
- inbound and outbound foreign keys
- rewrite triggers
- extra NOT NULL / no-default columns
- CHECK that is not enforced
- case-variant object names
- undersized types that revive a consumed value

A write that "ignores" a conflict is not replay detection.

## M7 — Test bites the shipped function

**When:** any new regression.

Revert the **shipped** function, not the helper the test imports.
If the suite stays green, the test is tautological.

Also require, when the claim needs them: a success control (the
good case still works); the cache-hit / stored path, not only
miss; the host filesystem the CI runner actually has.
