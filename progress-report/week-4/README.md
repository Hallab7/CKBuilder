# Week 4 — May 23–29 2026

Fourth week of the CKBuilder program. Built the CKB Attestation Protocol — a composable on-chain attestation system with custom Rust scripts, testnet deployment, and a full Next.js frontend.

## What I Did

### Designed the Project

Picked **On-Chain Attestation Protocol** as the week's project — a system for issuing verifiable, revocable attestations on CKB. Key design decisions:

- Two custom Rust scripts: `schema-type` (validates schema cells) and `attestation-type` (validates attestation cells)
- Attestations are owned by the **subject**, not the attester — first-class asset model
- Attesters can **revoke** by consuming the attestation cell (unlike Spore DOBs which are permanent)
- Explicit on-chain schema cells define the structure of each attestation type
- Transfer of attestations is explicitly blocked by the type script

### Built the On-chain Scripts (Rust)

**`schema-type`** — validates Schema Cell creation and updates:
- Creation (no GroupInput): validates data layout — name, description, fields_json must be non-empty
- Update (GroupInput exists): verifies the attester (from script args) signed the transaction
- Script args: `[attester_lock_hash: 32]`

**`attestation-type`** — validates Attestation Cell lifecycle:
- Creation: verifies attester signed, validates data layout matches args
- Revocation (input exists, no output): verifies attester signed
- Transfer (input + output): explicitly rejected with `TransferNotAllowed` error
- Script args: `[schema_id: 32][attester_lock_hash: 32]`

**Cell data layouts:**

Schema:
```
[name_len: 2 LE][name][desc_len: 2 LE][desc][fields_len: 2 LE][fields_json]
```

Attestation:
```
[schema_id: 32][attester_lock_hash: 32][issued_at: 8 LE u64][data_len: 2 LE][data_json]
```

### Wrote and Passed All 7 Tests

| Test | Result |
|------|--------|
| `test_create_schema` | ✅ |
| `test_update_schema` | ✅ |
| `test_unauthorized_schema_update_fails` | ✅ |
| `test_issue_attestation` | ✅ |
| `test_revoke_attestation` | ✅ |
| `test_unauthorized_revoke_fails` | ✅ |
| `test_transfer_attestation_fails` | ✅ |

### Deployed Scripts to Testnet

Stripped debug symbols (20MB → 42KB), deployed using `ckb-cli deploy`:

| Script | Code Hash | Tx |
|--------|-----------|-----|
| schema-type | `0x4414ee08...a34b` | `0x43649fed...7f58` index 0 |
| attestation-type | `0x088b2248...3957` | `0x43649fed...7f58` index 1 |
| dep-group | — | `0x8a11e0b8...7ae0` index 0 |

Both transactions committed on testnet.

### Built the Frontend (Next.js + CCC SDK)

**Pages:**
- `/` — Home with how-it-works and recent schemas
- `/schemas` — Browse all schemas on testnet with search
- `/schemas/[schemaId]` — Schema detail with field definitions and issued attestations
- `/attestations/[attestationId]` — Attestation detail with all data, revoke button for attester
- `/issue` — Create a new schema with dynamic field builder (name, type, required)
- `/issue/[schemaId]` — Issue attestations to recipients, form auto-generates from schema fields
- `/wallet` — Three tabs: Received / Issued by me (with revoke) / My Schemas
- `/verify` — Public verifier: paste any address, see all attestations it holds

**Key integrations:**
- `createSchema()` — builds schema cell with attester lock hash in type args
- `issueAttestation()` — builds attestation cell owned by subject, adds schema cell as cell dep
- `revokeAttestation()` — consumes attestation cell, CKB returned to attester
- Indexer queries by lock script (subject) and cell data (schema ID) for filtering
- `completeFeeBy(signer, 1000)` to handle MetaMask fee rate differences

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-attestation-protocol | On-chain attestation system with custom Rust scripts | [GitHub](https://github.com/Hallab7/ckb-attestation-protocol) |
| Live app | Deployed on Vercel, connected to CKB testnet | [attestckb.vercel.app](https://attestckb.vercel.app) |

## Key Concepts Learned

- Type scripts as data validators (not just state machines) — schema-type validates data format
- How to block specific operations in a type script (transfer → `TransferNotAllowed`)
- Using cell deps to reference related cells — schema cell as dep when issuing attestation
- The difference between revocable attestations (custom scripts) and immutable DOBs (Spore)
- How `Source::GroupInput` / `Source::GroupOutput` determine the operation type (create vs revoke vs transfer)
- Encoding/decoding mixed binary + JSON data in both Rust and TypeScript with matching byte layouts
- Why the unauthorized update test requires the attacker to NOT have the attester's lock hash in inputs

## Cycle Counts

| Action | Cycles |
|--------|--------|
| Create schema | ~35,000 |
| Update schema | ~38,000 |
| Issue attestation | ~42,000 |
| Revoke attestation | ~31,000 |
