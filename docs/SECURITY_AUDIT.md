# IntentQuorum security audit

## Scope

The audit covers proposal binding, semantic result handling, quorum
accounting, access control, replay, prompt injection, and state transitions.

## Findings and controls

| Threat | Control |
| --- | --- |
| Proposal mutation | No update method; all authority inputs are fingerprinted. |
| Reusable natural-language authority | Approval binds proposal fingerprint, signer, and exact text. |
| Duplicate signer inflation | One approval slot per `(proposal_id, approver)`; duplicate submissions revert. |
| Unauthorized approval | Caller address must be in the frozen approver set. |
| Raw-record quorum | `qualified_approval_count` increments only from the deterministic projection rule. |
| Model-controlled quorum | The projection cannot contain `counts_for_quorum`; the contract derives it. |
| Contradictory amount/source/constraint | Covering and compatibility relations are required for counting. |
| Prompt injection | Proposal and approval values are delimited as untrusted data; malformed or unsafe output is non-counting. |
| Validator disagreement | Strict projection normalization and validator equality are required; failure stores `UNRESOLVED`. |
| Executor spoofing | Only the frozen executor can consume. |
| Fingerprint replay | Consumption requires the exact proposal fingerprint and succeeds only from `AUTHORIZED`. |
| State rollback | Writes only implement `OPEN -> AUTHORIZED -> CONSUMED`. |
| Fake expiry | No expiry field or time claim is exposed in v1. |
| Arbitrary external execution | Deliberately out of scope; consumption stores proof only. |

## Residual assumptions

The protocol relies on GenLayer consensus and the selected runtime's
nondeterministic execution. Semantic classification remains bounded policy,
not legal interpretation or proof that a human understood a proposal. A
consumer must verify the full proposal fingerprint and decide how to act on a
consumed authorization.
