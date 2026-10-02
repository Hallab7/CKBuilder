# Week 19 - September 15-21 2026

Nineteenth week of the CKBuilder program. Continued building **ReLoop Rewards** by connecting operator-submitted collection events to deterministic validation, manual review, exactly-once reward reservation, and a guarded Fiber settlement boundary.

This week focused on financial correctness. An accepted event must reserve one reward, a payout must follow an explicit state machine, retries must reuse the same record, and an unknown Fiber outcome must stop for reconciliation instead of sending another payment.

## What I Did

### Built the Deterministic Validation Engine

Implemented a validation service that evaluates collection events against the activated campaign rules and a complete eligibility context.

Validation checks include:

- Active campaign status
- Authorized operator assignment
- Active participant status
- Current participant consent
- Duplicate event detection
- Material and measurement-unit matching
- Minimum and maximum quantity
- Participant and campaign event limits
- Campaign start and end times
- Event clock-skew allowance
- Remaining campaign reward budget

Accepted rewards are calculated from integer quantity and reward-rate values. Rejected events return stable rule codes without exposing private participant information.

### Added Manual Review Decisions

Implemented a review queue for events that need a human decision before acceptance.

The review workflow supports:

- Pending review records
- Explicit reviewer identity
- Accepted or rejected decisions
- Required reason codes
- Decision timestamps
- Rejection of repeated decisions
- Preservation of the original material, quantity, and reward inputs

Reviewers can decide whether an event is acceptable, but they cannot silently rewrite the campaign reward calculation.

### Implemented Exactly-Once Reward Reservation

Added a reward acceptance ledger that turns a valid event into one budget reservation.

Reservation now enforces:

- Required event, event-key, and campaign identity
- Positive reward amounts
- Remaining campaign budget
- Campaign event limits
- Existing reservation reuse for a repeated event key
- One durable payout path per accepted event

This separates event acceptance from payment submission. The reward is accounted for before an external spending call begins.

### Built the Payout State Machine

Implemented payout records and explicit legal transitions from reservation through terminal status.

Payout states include:

- Reserved
- Invoice created
- Submitted
- Confirming
- Paid
- Failed
- Ambiguous
- Cancelled

Each payout receives a deterministic idempotency key derived from its event. Invalid transitions are rejected, and duplicate reservation requests return the existing payout instead of creating another financial obligation.

### Added the Versioned Fiber Boundary

Built a Fiber adapter around an injected transport interface so node-specific RPC behavior remains outside the domain services.

The adapter validates:

- Receiver reference
- Positive integer amount
- Configured RUSD asset identifier
- Invoice description
- Invoice expiration
- Required payment idempotency key
- Normalized payment status values

Node responses such as pending, in-flight, processing, completed, rejected, and cancelled are mapped into a smaller application state model. Unknown responses remain unknown rather than being treated as success.

### Implemented Settlement and Failure Classification

Connected reserved payouts to invoice creation, payment submission, confirmation lookup, and final payout status.

The settlement engine:

1. Requires an existing reserved payout.
2. Requests an invoice for the committed receiver, amount, and asset.
3. Rejects an invoice that does not match the payout.
4. Submits payment with the existing idempotency key.
5. Records failed, submitted, confirming, paid, or ambiguous outcomes.
6. Stops automatic progress when no reliable payment hash or terminal result is available.

This implementation proves the application control flow against a transport contract. It does not prove that a funded Fiber node transferred RUSD on testnet.

### Added Ambiguous-Payment Reconciliation

Implemented a recovery path for payments whose spending result cannot be established immediately.

An ambiguous payout:

- Is shown as needing reconciliation
- Cannot be submitted again as a fresh payout
- Can only be resolved to paid or failed through reconciliation
- Preserves its original event, amount, asset, receiver, and idempotency key
- Remains separate from ordinary retryable transport failures

This avoids the dangerous assumption that a timeout means no money moved.

### Built Participant and Sponsor Reporting

Added participant-safe payout history and campaign-level recovery summaries.

Participant status maps internal records into:

- Pending
- Paid
- Failed
- Needs reconciliation

Sponsor summaries aggregate accepted, rejected, and review event counts; accepted physical quantity; participant count; and reserved, paid, failed, and ambiguous reward totals. The totals are calculated from source records rather than maintained as an independent mutable balance.

### Verified Financial Safety

Verification covered:

- Positive and negative event-validation rules
- Deterministic reward calculation
- Review decisions and repeated-decision rejection
- Budget and event-limit enforcement
- Retry-safe acceptance reservations
- One payout per event
- Payout transition restrictions
- Fiber invoice and idempotency validation
- Payment-state normalization
- Successful, failed, and ambiguous settlement outcomes
- Reconciliation without a second payment submission
- Participant-safe payout history
- Campaign recovery and payment summaries

All checks are deterministic local tests. A funded Fiber payer, compatible recipient, RUSD liquidity, and confirmed testnet payment hashes are still required before live settlement can be claimed.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-reloop-rewards | Recycling reward platform with deterministic event validation, manual review, exactly-once reward reservation, guarded Fiber settlement, ambiguous-payment reconciliation, and participant/sponsor reporting | [GitHub](https://github.com/Hallab7/ckb-reloop-rewards) |

## Key Concepts Learned

- Why deterministic validation should produce stable rule codes instead of ad hoc error text
- How integer reward calculation avoids floating-point accounting errors
- Why manual review must not allow reward inputs to be rewritten
- How accepting an event and paying it are separate financial transitions
- Why a budget reservation should exist before the first external spending call
- How event uniqueness and payment idempotency prevent different forms of duplicate reward
- Why an invoice must match the reserved receiver, amount, asset, and expiry requirements
- How an adapter isolates changing Fiber RPC states from application state
- Why missing payment evidence must become an ambiguous state rather than an automatic retry
- How reconciliation resolves uncertainty without creating a second payment
- Why reporting totals should be derived from durable event and payout records
- Why passing transport-contract tests is not proof of a funded Fiber testnet transfer
