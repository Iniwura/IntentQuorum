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
the exact source SHA recorded after all gates pass. The final report must
distinguish finalized deployment, source readback, and any live smoke subset;
transaction hashes alone are not treated as deployment proof.

## Limitations

IntentQuorum proves validator consensus that an approval covers an exact
proposal under a bounded semantic policy. It does not prove human
understanding, off-chain legal authority, or execution of arbitrary off-chain
actions. It is an authorization primitive, not a wallet frontend, token, or
generic governance system.
