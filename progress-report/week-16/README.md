# Week 16 - August 24-31 2026

Sixteenth week of the CKBuilder program. Continued building **SignalBounty** by extending submission validation into contributor eligibility, payout readiness, mock Fiber settlement, and settlement safety controls.

This week focused on making accepted measurements payable exactly once in local mode while keeping payer credentials server-side and separating mock evidence from funded testnet acceptance.

## What I Did

### Completed Manual Review and Audit Recording

Expanded the validation workflow with a review queue for suspicious but not automatically invalid submissions.

Manual review now supports:

- Review-pending submission listing
- Campaign-specific review filtering
- Reviewer approval
- Reviewer rejection
- Manual reason codes
- Audit events for each decision
- Export row hash creation only when a reviewed sample is accepted

This gives operators a way to handle outliers without turning every unusual measurement into an automatic rejection.

### Implemented Contributor Enrollment and Consent

Added contributor profile workflows for the controlled pilot.

Contributor eligibility now includes:

- Pilot invite claiming
- Optional cohort and app-install references
- Pending and active consent states
- Campaign-specific consent records
- Requested metric snapshots
- Retention policy version tracking
- Submission blocking when consent is missing
- Contributor submission history lookup

The submission API now rejects measurements from contributors who are unknown, disabled, or missing active consent for the selected campaign.

### Added Payout Profile Handling

Built the payout profile path required before an accepted sample can create a reward.

The payout profile stores:

- Testnet settlement mode
- Approved Fiber receiver public key
- Compatibility-check timestamp
- Payout-disabled flag

The Android and browser boundaries never receive payer-node credentials. The only stored contributor settlement field is the approved receiver reference needed by the server-side worker.

### Built the Mock Fiber Settlement Adapter

Implemented a versioned local settlement boundary for RUSD testnet behavior.

The Fiber adapter now supports:

- RUSD asset configuration validation
- Lowercase 32-byte code-hash checks
- Receiver public-key validation
- Privacy-safe invoice descriptions
- Exact amount validation
- Exact asset validation
- Receiver validation
- Invoice expiry checks
- Payment fee quote
- Mock payment submission
- Sender and receiver payment hashes
- Normalization of version-specific payment states

The adapter stays behind server-side API and worker code, matching the intended production trust boundary.

### Added Exactly-Once Payout Reservation

Connected accepted measurements to payout rows in the store.

Payout reservation enforces:

- Accepted and payable measurement requirement
- One payout per accepted submission
- Contributor payout profile requirement
- Campaign reward amount
- RUSD testnet asset ID
- Unique idempotency key
- Campaign budget cap
- Existing payout reuse for duplicate reservation attempts

Rejected and review-pending submissions cannot create payouts.

### Implemented Settlement Worker and API Route

Added the local settlement job and internal settlement route.

Settlement now performs:

1. Payout lookup.
2. Campaign lookup.
3. Contributor payout profile lookup.
4. Invoice creation.
5. Invoice validation.
6. Payout status update.
7. Payment submission through the adapter.
8. Sender and receiver evidence storage.
9. Paid, failed, or ambiguous status classification.

Running the same settlement job again for an already paid payout returns the existing result instead of submitting another payment.

### Added Payout Safety Controls

Implemented store-level settlement controls that are checked by both the worker and the internal API route.

The controls include:

- Global emergency stop
- Campaign payout hold
- Contributor payout hold
- Maximum Fiber fee cap
- Audit events for administrator updates
- No automatic retry for ambiguous spending results

The fee cap is checked after invoice validation and before payment submission, so an expensive route can be blocked without sending funds.

### Verified Settlement Safety

Verification covered:

- Fiber adapter unit tests
- API contributor and payout tests
- Worker settlement tests
- Duplicate settlement behavior
- Unknown payment quarantine
- Emergency stop blocking
- Fee-cap blocking
- TypeScript package build
- Next.js production build

The focused settlement test run passed 19 tests across the Fiber adapter, API, and worker settlement files. Live RUSD transfer evidence was not claimed because funded payer and receiver nodes, RUSD liquidity, and compatibility fixtures were not available.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-signalbounty | CKB and Fiber testnet mobile-network measurement marketplace with contributor consent, payout profiles, reserved rewards, mock Fiber settlement, emergency controls, and audited review decisions | [GitHub](https://github.com/Hallab7/ckb-signalbounty) |

## Key Concepts Learned

- Why contributor consent must be campaign-specific before accepting measurement uploads
- How payout profiles separate receiver setup from payer-node custody
- Why an accepted sample should reserve a payout before any spending call
- How idempotency keys and submission uniqueness prevent duplicate rewards
- Why invoice amount, asset, receiver, description, and expiry should be validated together
- How ambiguous payment responses can create financial risk if retried as new payments
- Why emergency controls should live below route handlers so workers enforce the same rules
- How fee quotes support spending safety before payment submission
- Why browser and Android code must never call Fiber payer RPC directly
- How local settlement evidence can prove control flow while still requiring live testnet confirmation later
