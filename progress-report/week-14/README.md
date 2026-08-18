# Week 14 - August 8-15 2026

Fourteenth week of the CKBuilder program. Continued building **SkillMinute** by connecting signed session limits to private live rooms, authoritative presence, guarded Fiber settlement, canonical receipts, CKB commitments, participant dashboards, and audited administration.

This week focused on proving that browser timers cannot trigger spending, one interval cannot be paid twice, both participants receive the same receipt, and operators can stop new settlement safely.

## What I Did

### Completed the Authorization Experience

Added the learner-facing authorization screen and protected session APIs.

Before signing, the learner sees:

- Expert and session topic
- Scheduled start time
- Free introduction allowance
- Exact five-minute interval rate
- Maximum interval count
- Maximum possible testnet charge
- Started-interval refund rule
- Operator-assisted testnet notice

The screen signs the canonical server payload rather than reconstructing financial terms in browser code. Route handlers call the guarded command service and cannot directly change session state.

### Built Private LiveKit Session Rooms

Implemented one private LiveKit room for each booked session.

Room access now enforces:

- Booked-participant membership
- Short-lived room tokens
- Room-specific join, publish, and subscribe grants
- Rejection after session completion
- Signed LiveKit webhook validation
- Authenticated participant heartbeats
- Server timestamps and reconnect grace

The room interface displays participant media, connection state, server-derived session status, free-introduction timing, current paid interval timing, amount paid, authorized cap, pause state, and end controls.

Browser countdowns are projections only. They cannot create or advance a paid interval.

### Added Authoritative Presence Processing

Created durable presence records that combine verified LiveKit events with authenticated heartbeats.

Before starting another paid interval, the worker confirms that:

- The learner is still present
- The expert is still present
- Both heartbeats are recent
- The reconnect grace window has not expired

A temporary disconnect does not refund an already-started interval, but it blocks the next payment. Refreshing or reconnecting resumes the existing interval without increasing its index.

### Implemented the Versioned Fiber RPC Boundary

Built a server-only JSON-RPC transport with:

- JSON-RPC request identifiers
- Request timeouts
- Runtime response validation
- Node-version compatibility checks
- Safe retries for read methods only
- Structured error categories
- Endpoint, token, and invoice redaction
- Environment-loaded RUSD configuration

Spending methods remain inside the worker. Fiber RPC endpoints and credentials are never returned to browser code.

### Built Idempotent Capped Settlement

Implemented the interval settlement service and PostgreSQL repository.

For every requested interval, the service now:

1. Checks the immutable learner authorization.
2. Confirms both participants are present.
3. Checks the interval count and total cap.
4. Applies global, expert, session, daily, and fee controls.
5. Reserves a unique session and interval index in the database.
6. Creates an exact receiver invoice with a privacy-safe commitment.
7. Validates amount, asset, receiver, description, and expiry.
8. Performs a route and fee dry-run.
9. Submits the payment once.
10. Confirms both sender payment state and receiver invoice state.

Ambiguous spending responses are quarantined and reconciled rather than retried as new payments. A unique database constraint ensures repeated API or worker requests cannot create another payout for the same interval.

### Added Settlement Safety and Credit Accounting

Implemented safety controls for:

- Global emergency stop
- One expert receiver
- One session
- Maximum Fiber fee
- Daily payout cap
- Ambiguous-payment review

Every administrator update requires an authenticated administrator, a reason, and a durable audit event.

Added a prepaid testnet credit ledger for deposits, reservations, captures, releases, and refunds. This mode remains disabled until sponsored settlement succeeds with real testnet evidence.

### Generated Canonical Privacy-Safe Receipts

Built one canonical receipt body shared by the learner and expert.

Receipts include:

- Pseudonymous participant references
- Authorization terms hash
- Scheduled, start, and end timestamps
- End reason
- Paid interval count
- Total paid base units
- Fiber sender payment hashes
- Receipt schema version

Receipts exclude call content, problem descriptions, email addresses, IP addresses, private endpoints, keys, and invoice preimages.

Optional learner and expert signatures attach to the same receipt hash rather than creating separate receipt versions.

### Built Deterministic CKB Commitment Publishing

Created deterministic receipt batching and proof generation.

The commitment workflow:

- Sorts completed receipt hashes
- Builds a deterministic Merkle tree
- Stores a proof for every receipt
- Encodes the `SKMN` marker, schema version, UTC date, receipt count, and root
- Uses CCC to construct and submit an operator-controlled CKB testnet data cell
- Stores the transaction hash and output index
- Runs automatically for the completed UTC day

Added a public receipt verification page and command-line tool that check the canonical SHA-256 hash, Merkle inclusion proof, listed Fiber payment hashes, and CKB cell data.

The publishing adapter and verification path are implemented, but a real commitment is not claimed without a funded testnet signing key.

### Built Learner, Expert, and Administrator Dashboards

The learner dashboard shows:

- Upcoming bookings
- Session history
- Authorized cap and paid total
- Receipt commitment status
- Prepaid testnet credit status
- Post-session feedback form

The expert dashboard shows:

- Profile and receiver configuration
- Availability management
- Upcoming sessions
- Paid intervals and testnet earnings
- Payment states and hashes
- Shared receipts
- Published rating summary

The administrator dashboard shows:

- Active sessions and presence
- Interval state
- Compatibility target
- Payment failures and ambiguous results
- Daily payout total
- Pilot cohort and notification status
- Current settlement controls
- Emergency-stop configuration

Learner and expert queries are restricted to their own records. Private structured feedback is never included in public rating responses. Public comments and scores remain hidden until both participants submit or the seven-day review window closes.

### Added Durable Storage and Worker Automation

Added an incremental migration for:

- Session presence
- Settlement controls
- Credit entries
- Canonical receipts
- Participant signatures
- Commitment batches
- Merkle proofs
- Ratings

The worker now handles presence expiry, payment reconciliation, new interval settlement, missing receipt generation, and daily CKB publishing. Commitment signing is disabled unless its explicit environment switch and private key are supplied.

### Verified the Complete Implementation

Verification results:

- 177 automated tests passed across 15 test files
- TypeScript checks passed
- Prisma schema validation passed
- Next.js production build completed successfully
- All 20 static pages generated successfully
- Authorization, presence, settlement, receipt, Merkle, credit, and rating tests passed
- Commit subjects were checked against the reporting constraint

The remaining external acceptance work requires running PostgreSQL and LiveKit services, funded Fiber testnet payer and receiver nodes, compatible RUSD channels and liquidity, and a funded CKB testnet commitment key. No live transfer or on-chain transaction is invented in this report.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-skillminute | Private expert-help sessions with signed caps, authoritative presence, idempotent Fiber settlement, canonical receipts, CKB commitments, and role-scoped dashboards | [GitHub](https://github.com/Hallab7/ckb-skillminute) |

## Key Concepts Learned

- Why browser timers must never authorize or trigger settlement
- How signed webhooks and authenticated heartbeats provide stronger presence evidence together
- Why a reconnect must resume the same interval instead of allocating a new one
- How durable reservation before RPC submission prevents duplicate payouts
- Why ambiguous spending responses must be quarantined instead of blindly retried
- How sender and receiver evidence strengthen payment reconciliation
- Why emergency controls need global, expert, session, fee, and daily scopes
- How canonical receipts give both participants byte-identical evidence
- Why private session content does not belong in a public CKB commitment
- How deterministic Merkle proofs reduce on-chain storage while preserving independent verification
- Why public rating visibility needs a mutual-review or timed-release rule
- How role-scoped queries prevent dashboards from leaking another participant's records
- Why live-network acceptance must remain separate from local implementation evidence
