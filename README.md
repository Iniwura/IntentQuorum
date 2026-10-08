# IntentQuorum

IntentQuorum is a semantic multisig for GenLayer. A proposal freezes one
bounded executable action, a fixed approver set, an N-of-M threshold, and a
designated executor. Approvers may describe approval in their own words, while
GenLayer validators compare each intent to the exact immutable proposal.

This is not generic text similarity or LLM voting. The model returns only a
strict structural projection. The contract derives quorum eligibility:

```text
COMPLETE
and action_match
and target_match
and resource_match
and amount_relation == COVERS
and purpose_relation == COVERS
and source_relation == COVERS
and constraint_relation == COMPATIBLE
and ambiguity == false
```

Malformed JSON, extra or missing keys, invalid enums, validator disagreement,
tentative language, and contradictory constraints fail closed into a stored
`UNRESOLVED` non-counting approval. A signer can submit at most one immutable
approval per proposal. The approval fingerprint binds the exact signer and
text to the proposal fingerprint, so natural-language text never becomes
reusable generic authority.

## Action packet and lifecycle

The packet contains `action_type`, `action`, `target`, `resource`, `amount`,
`purpose`, `source`, and `constraints`. Proposal creation separately receives
the approver addresses, threshold, and executor. The lifecycle is:

```text
OPEN -> AUTHORIZED -> CONSUMED
```

There is no contract-enforced expiry in v1. The current Studio Dev runtime did
not provide an authoritative contract clock that could be safely enforced, so
the design deliberately has no unenforced deadline field.

`CONSUMED` is an authorization proof only. IntentQuorum does not execute
arbitrary external calls, move funds, or infer an off-chain action. A consumer
must verify the stored proposal fingerprint and consumption state before doing
its own action.

## Public methods

Writes: `create_proposal`, `submit_approval`, and `consume_authorization`.

Views: `get_proposal`, `get_approval`, `get_proposal_approval_ids`,
`get_approval_for_approver`, `is_authorized`, and `get_proposal_fingerprint`.

The checked-in schema is [schema/intent_quorum.schema.json](schema/intent_quorum.schema.json).

## Security boundary and limitations

IntentQuorum proves validator consensus that a natural-language approval covers
an exact proposal under the contract's bounded semantic policy. It does not
prove that a human understood the proposal, infer off-chain legal authority,
or execute arbitrary off-chain actions itself. The protocol assumes the
GenLayer validator set and runtime provide the consensus guarantees of the
selected network, and that consumers verify the exact proposal fingerprint.

Proposal and approval strings are untrusted data. The evaluator prompt
explicitly isolates them, and the contract rejects model-provided quorum
booleans. Configuration has no update path; any meaningful change requires a
new proposal. Bounds include 16 approvers, 2,000 approval-text characters, and
bounded action fields.

## Development

The project targets Studio Dev (`chainId 61997`) and pins the verified runner:

```text
py-genlayer:5jycge4q8k23462jtb0b9fyey1s9qz928sz2nbrd9mg4sxqg2qng
GENVM_VERSION=vstudio-dev
genlayer-py==0.16.3
genlayer-test==0.29.2
genvm-linter==0.11.0
pyright==1.1.411
```

With `/home/ini/materialproof/.venv/bin` on `PATH`:

```bash
pytest
GENVM_VERSION=vstudio-dev genvm-lint check contracts/intent_quorum.py
GENVM_VERSION=vstudio-dev genvm-lint schema --json contracts/intent_quorum.py
GENVM_VERSION=vstudio-dev genvm-lint typecheck contracts/intent_quorum.py
pyright
git diff --check
```

The adversarial suite covers proposal bounds and immutability, signer and
executor access control, positive and negative semantic projections, prompt
injection data isolation, malformed consensus output, validator disagreement,
quorum counting, replay, cross-proposal binding, and single-use consumption.

The frozen implementation has 67 passing direct-harness tests. The checked-in
schema matches fresh extraction, and the packaged typecheck and direct Pyright
check are clean.

## Studio Dev deployment

- Network: Studio Dev (`61997`)
- Contract: [`0x3673225E4652880E4b8238A3B60222c499b46648`](https://explorer-studio.genlayer.com/address/0x3673225E4652880E4b8238A3B60222c499b46648)
- Deployment transaction: `0xac221273a16e09ef8925af648fb820d8a7e2f4faca6446de443175d95c83868b`
- Finalized result: `ACCEPTED` / `MAJORITY_AGREE`, `FINISHED_WITH_RETURN`
- Frozen source SHA-256: `17f4a87acb1e6684d39c659a74eea563f592b10f9ed4db8d69ce6b7387f4e6a0`
- Deployment source readback matched the local source exactly after removing the CLI wrapper and trailing blank lines.

The controlled live smoke test passed with proposal `live-iq-20261008-a`: two distinct approvals reached threshold 2, the designated executor consumed it, and a replay finalized as the expected contract error. See [`PORTAL.md`](PORTAL.md) and [`SUBMISSION.md`](SUBMISSION.md) for the complete evidence record.
