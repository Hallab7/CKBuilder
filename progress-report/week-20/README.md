# Week 20 - September 22-28 2026

Twentieth week of the CKBuilder program. Completed the documented local release of **ReLoop Rewards** with privacy-controlled evidence, reproducible exports, CKB commitment payloads, operational safeguards, pilot fixtures, reconciliation, deployment guidance, and release handover documentation.

This week focused on evidence quality and operational honesty. The repository can prove its local rules and deterministic outputs, but it does not claim a real recycling pilot, funded RUSD settlement, or confirmed CKB transaction until those external records exist.

## What I Did

### Added Private Collection Evidence Controls

Implemented the storage contract for optional collection photos and other private evidence.

Evidence registration requires:

- Explicit participant consent
- A campaign and collection-event association
- A private object-storage key
- JPEG or PNG content type
- A positive size below the configured 10 MB limit
- A future retention-expiry timestamp
- Campaign-scoped lookup

Expired evidence can be deleted without deleting the minimum collection, payout, and audit records. Raw photos and private object URLs are excluded from public exports and CKB commitment payloads.

### Built Privacy-Filtered Recovery Exports

Added deterministic CSV generation and export manifests for sponsor and audit review.

The export path provides:

- Stable event ordering
- Proper CSV escaping
- Participant codes instead of private participant records
- Material, quantity, unit, status, and date bucket
- Rules hash
- Export row count
- File hash
- Privacy-policy version
- Generation timestamp

The manifest lets a reviewer verify which file was produced from which campaign-rule version without publishing QR secrets, evidence photos, payer credentials, or private infrastructure details.

### Implemented CKB Commitment Payloads

Built deterministic data structures for future CKB testnet anchoring.

The commitment package includes:

- Campaign-rule payloads with hashed campaign and site references
- Canonical JSON key ordering
- SHA-256 payload hashes
- Daily collection leaves
- Merkle-root construction
- Merkle inclusion proofs and verification
- Sorted payout-summary payloads
- Paid, failed, and ambiguous amount fields
- Ordered payment-hash references

The tests prove deterministic construction and inclusion verification. The package does not submit a CCC transaction or provide a confirmed testnet transaction hash, so the result is reported as commitment preparation rather than live anchoring.

### Added Operational Safety Controls

Implemented controls that apply before collection or payout work proceeds.

Administrators can:

- Pause a campaign
- Revoke an operator
- Enable or disable the payout emergency stop
- Set a maximum payout exposure
- Record actor, scope, reason, and timestamp for each control action

Collection checks reject paused campaigns and revoked operators. Payout checks reject spending while the emergency stop is active or when the amount exceeds the configured exposure limit.

### Added Privacy-Safe Notifications

Built deduplicated participant, operator, and sponsor notifications for important terminal and operational outcomes.

Notification codes cover:

- Event accepted or rejected
- Payout pending, paid, or failed
- Campaign paused
- Campaign budget warning

Messages do not contain payer credentials, QR secrets, private evidence links, or raw technical error responses.

### Built the Role Workspace and Deployment Runbooks

Added a responsive web shell for participant, operator, sponsor, and administrator workspaces, together with local and testnet deployment documentation.

The runbooks cover:

- Environment and dependency startup
- Release checks
- Role-specific routes
- Database, Redis, object-storage, payer, and signer separation
- Testnet safety flags
- Exposure limits
- Smoke checks
- Rollback using campaign pause and payout emergency stop
- Preservation of ambiguous payment and audit records

Mock Fiber and CKB behavior must remain visibly separate from funded testnet evidence.

### Added a Deterministic Pilot Fixture

Created a reproducible local dataset for testing reporting and reconciliation at the intended pilot scale.

The fixture contains:

- 50 synthetic participant codes
- 200 accepted collection events
- PET as the sample material
- Quantity 10 for each event
- Total synthetic quantity of 2,000 units

The fixture command verifies the participant and event targets before printing the summary. It contains no partner, operator, physical collection, Fiber payment, or CKB transaction evidence.

### Implemented Physical and Digital Reconciliation

Added a reconciliation report that compares:

- Physical quantity against accepted digital quantity
- Expected paid amount against recorded paid amount
- Quantity variance
- Payment variance
- Matched or variance status

Variances remain explicit rather than being silently adjusted. A real sponsor report will require physical totals signed off by the pilot partner.

### Completed Release and Handover Documentation

Documented the implemented modules, verification commands, ownership boundaries, and live evidence still required.

Operational ownership is separated across:

- Fiber payer funding and node health
- Participant and collection-site support
- Database, Redis, object storage, and backups
- CKB signer and commitment verification
- Privacy and retention requests
- Security incidents and emergency-stop authority

This makes the remaining work assignable instead of hiding it behind a generic deployment task.

### Verified the Local Release

The final release verification passed:

- Secret audit across 106 source files
- Type checks across applications and shared packages
- Strict TypeScript lint checks
- 68 workspace tests
- Production TypeScript builds for the web, API, and worker applications
- Deterministic pilot fixture with 50 participants and 200 accepted events
- Campaign commitment, Merkle proof, payout summary, privacy export, safety control, notification, and reconciliation tests

The automated release gate is green. Public-beta acceptance still requires a real partner site, funded Fiber testnet payer, confirmed RUSD payment hashes, confirmed CKB commitment transactions, partner-signed physical reconciliation, browser workflow recording, and named maintenance ownership.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-reloop-rewards | Locally verified CKB and Fiber recycling-reward implementation with private evidence controls, deterministic exports, commitment payloads, Merkle proofs, operational safeguards, pilot fixtures, reconciliation, and handover documentation | [GitHub](https://github.com/Hallab7/ckb-reloop-rewards) |

## Key Concepts Learned

- Why optional photos should remain private supporting evidence rather than public truth claims
- How retention deletion can remove private evidence without destroying financial audit records
- Why deterministic exports need stable ordering, canonical fields, and a file-hash manifest
- How Merkle roots let a daily event set be committed without publishing every private record
- Why building a commitment payload is different from confirming a CKB transaction
- How campaign pauses, operator revocation, emergency stops, and exposure limits reduce operational risk
- Why notifications should communicate outcomes without exposing credentials or private evidence
- How a large deterministic fixture tests scale and reporting without pretending to be a real pilot
- Why physical and digital quantities need an explicit variance report
- How release handover assigns custody, infrastructure, privacy, and incident responsibilities
- Why a green local release gate does not replace funded Fiber and confirmed CKB testnet evidence
