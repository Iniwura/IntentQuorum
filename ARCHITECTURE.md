# IntentQuorum architecture

IntentQuorum is a semantic multisig. A creator freezes one bounded action
packet, an immutable approver set, a threshold, and a designated executor.
Each approver may submit one natural-language intent. GenLayer independently
evaluates that intent against the exact packet and agrees only on a bounded
semantic projection. The contract, never the model, derives whether that
projection counts toward quorum.

The lifecycle is `OPEN -> AUTHORIZED -> CONSUMED`. There is no expiry in v1:
the current Studio Dev runtime does not expose a contract clock that this
protocol can safely enforce. There are no update methods; a changed action,
amount, approver set, threshold, or executor requires a new proposal.

The proposal fingerprint includes the schema/profile version, proposal ID,
all action-packet fields, the sorted approver set, threshold, executor, and an
explicit null expiry slot. The creator is audit metadata and is intentionally
not in the authority fingerprint. Each approval fingerprint additionally
binds the approver and exact text to the proposal fingerprint.

Semantic results are exactly nine fields: four booleans, four bounded relation
or status fields, and an ambiguity flag. Extra keys, missing keys, malformed
JSON, invalid enums, and validator disagreement become an unresolved,
non-counting approval. The deterministic rule requires `COMPLETE`, every
identity boolean, `COVERS` for amount/purpose/source, `COMPATIBLE` constraints,
and `ambiguity == false`.

Consumption is a proof primitive only. The designated executor supplies the
exact proposal fingerprint once, moving the proposal to `CONSUMED` and storing
a consumption fingerprint. IntentQuorum does not execute arbitrary external
calls or transfer funds.
