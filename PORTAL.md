# IntentQuorum portal

## Reviewer quick path

1. Read the immutable-state and consensus design in
   [ARCHITECTURE.md](ARCHITECTURE.md).
2. Inspect the exact public schema in
   [schema/intent_quorum.schema.json](schema/intent_quorum.schema.json).
3. Run the pinned local gates from the README.
4. Review the threat model in [docs/SECURITY_AUDIT.md](docs/SECURITY_AUDIT.md).
5. Use the deployment record below only after checking finalized receipt and
   deployed-source readback.

## Deployment record

```text
network: Studio Dev
chain_id: 61997
deployment_status: finalized and accepted
deployment_tx: 0xac221273a16e09ef8925af648fb820d8a7e2f4faca6446de443175d95c83868b
contract_address: 0x3673225E4652880E4b8238A3B60222c499b46648
fee_value: 100000000000010352 wei (0.100000000000010352 GEN)
source_sha256: 17f4a87acb1e6684d39c659a74eea563f592b10f9ed4db8d69ce6b7387f4e6a0
consensus_status: ACCEPTED / MAJORITY_AGREE (3 agrees, 2 idle)
execution_status: FINISHED_WITH_RETURN
source_readback: genlayer code readback matched local source after CLI wrapper/trailing blank-line normalization
source_sha_match: true
live_smoke_status: passed
live_proposal_id: live-iq-20261008-a
live_proposal_fingerprint: db40d8e9c045520ce55ccc152eaf3bee493064f15f91ba27b927b2604e3eda0f
live_qualified_count: 2 of threshold 2
live_approval_ids: 25e920bc94b9a042d42f3d2d6a8177a956da2d19ecfc4e79e94bdbf6ff53de63, 2a75e35d7dd825de9be74cd6b6e39d6fe11badf0ec67037c19076130086641e0
live_second_approval_tx: 0x15d13419ea7f43cdba450590d5604cbd1283543f706ac88f309d10ea0249ce29
live_consume_tx: 0x08a411fa11295ea09c94d7a3ef498eeedde3f8dcab992eaf146c7b0257921f4d
live_final_state: CONSUMED
live_consumption_fingerprint: 07532de410d5858e46648ccdaabd6e91e573100fc131bc9406ecc8497db3b3f4
replay_test: PASS; 0xc28bf584235e5214ac23de760a7aa727b432150bc39b84c0849970fbd50a3ee7 finalized ACCEPTED / FINISHED_WITH_ERROR with proposal-is-not-authorized rejection
```

Explorer: https://explorer-studio.genlayer.com/address/0x3673225E4652880E4b8238A3B60222c499b46648

No private keys, passwords, keystores, or deployment caches belong in this
repository.
