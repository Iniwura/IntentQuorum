# IntentQuorum submission

IntentQuorum demonstrates a semantic multisig primitive: free-form approval
intent is interpreted against one frozen action packet, then a deterministic
N-of-M quorum authorizes that exact fingerprint once.

## Why GenLayer

The semantic projection requires independent validator execution over natural
language. The contract retains only bounded fields and derives the final
counting boolean. Conventional deterministic smart-contract execution cannot
perform that interpretation by itself.

## Frozen design

- Lifecycle: `OPEN -> AUTHORIZED -> CONSUMED`.
- No expiry: Studio Dev provides no contract clock primitive that this v1 can
  enforce authoritatively.
- Writes: `create_proposal`, `submit_approval`, `consume_authorization`.
- Views: six compact audit and authorization views.
- Proposal identity: SHA-256 over schema/profile, proposal ID, action packet,
  sorted approvers, threshold, executor, and the explicit null expiry slot.
- Approval identity: SHA-256 over proposal ID, proposal fingerprint, signer,
  and exact approval text.
- Quorum: only a strict `COMPLETE` projection satisfying every deterministic
  covering/compatibility predicate counts.

## Evidence

Local gates and deployment details are kept in [PORTAL.md](PORTAL.md), with
the exact source SHA recorded after all gates pass. The final report
distinguishes finalized deployment, source readback, and the live smoke subset;
transaction hashes alone are not treated as deployment proof.

Deployment evidence:

- Network: Studio Dev (`61997`)
- Contract: `0x3673225E4652880E4b8238A3B60222c499b46648`
- Deployment transaction: `0xac221273a16e09ef8925af648fb820d8a7e2f4faca6446de443175d95c83868b`
- Result: `ACCEPTED` / `MAJORITY_AGREE`; execution `FINISHED_WITH_RETURN`
- Frozen source SHA-256: `17f4a87acb1e6684d39c659a74eea563f592b10f9ed4db8d69ce6b7387f4e6a0`
- Source readback: exact match with the local source after removing only the CLI response wrapper and trailing blank lines.
- Local verification: 67 direct-harness tests passed; lint, validation, schema extraction, packaged typecheck, and Pyright passed.
- Controlled live smoke test passed against the deployed address: proposal `live-iq-20261008-a`, three approvers, threshold 2, two distinct approvals, `qualified_approval_count: 2`, executor consume, and final state `CONSUMED`.
- Successful consume transaction: `0x08a411fa11295ea09c94d7a3ef498eeedde3f8dcab992eaf146c7b0257921f4d`.
- Replay transaction `0xc28bf584235e5214ac23de760a7aa727b432150bc39b84c0849970fbd50a3ee7` finalized with `FINISHED_WITH_ERROR` and the expected `proposal is not authorized` rejection.
- Full live smoke details, fingerprints, approval IDs, and the second approval transaction are recorded in `PORTAL.md`.

## Limitations

IntentQuorum proves validator consensus that an approval covers an exact
proposal under a bounded semantic policy. It does not prove human
understanding, off-chain legal authority, or execution of arbitrary off-chain
actions. It is an authorization primitive, not a wallet frontend, token, or
generic governance system.
