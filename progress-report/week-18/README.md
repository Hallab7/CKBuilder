# Week 18 - September 8-14 2026

Eighteenth week of the CKBuilder program. Started building **ReLoop Rewards**, an open-source CKB and Fiber testnet recycling-incentive platform that connects operator-verified collection events to reserved participant rewards and auditable recovery records.

This week focused on defining the trust boundary and building the foundation for campaigns, participant identity, consent, collection-site authorization, and idempotent event intake. The system treats physical verification as an operator responsibility and keeps payer credentials, QR secrets, and private evidence outside the browser.

## What I Did

### Defined the Pilot and Trust Boundaries

Converted the ReLoop Rewards concept into a focused testnet workflow for one sponsor organization, one collection site, and a controlled recycling campaign.

The initial boundary establishes that:

- An authorized operator verifies the physical collection
- Software validates campaign rules, eligibility, limits, and duplicates
- RUSD rewards are denominated in integer base units
- Fiber payer credentials remain server-side
- CKB commitments contain hashes and public totals rather than participant identities or photos
- Testnet payments do not represent mainnet monetary value
- A collection record is not a carbon credit or environmental certificate

This prevents the application from presenting software validation as proof that a physical recycling event occurred.

### Created the ReLoop Rewards Workspace

Built an npm workspace separating the web application, API, worker, and shared domain packages.

The repository foundation includes:

- Participant, operator, sponsor, and administrator web boundaries
- Independent API and worker processes
- Shared TypeScript configuration
- Workspace-level type checking, linting, tests, and builds
- Docker Compose and environment templates
- A secret-audit command for committed source files
- Separate packages for contracts, configuration, authorization, campaigns, participants, events, and later settlement services

This structure keeps user-facing code separate from payout credentials and background processing.

### Added Typed Configuration and Money Rules

Implemented startup configuration validation for the services and shared integer money utilities.

Configuration checks cover:

- Database, Redis, and private object-storage settings
- Session and QR signing secrets
- Fiber RPC endpoint and authentication
- RUSD asset identifier and precision
- CKB network and RPC settings
- Environment-specific testnet safeguards
- Redacted configuration summaries that exclude secrets

Reward values use integer base units. Unsafe numbers, malformed asset identifiers, negative amounts, and invalid campaign limits are rejected before they reach settlement logic.

### Defined the Durable Data Model

Added a Prisma schema for the records required to preserve campaign, collection, payout, and audit state.

The schema covers:

- Users, sessions, organizations, sites, and operator assignments
- Campaigns, rule versions, budgets, and review decisions
- Participants, invitations, signed QR identities, and consent records
- Collection events, validation findings, and review results
- Payout reservations and Fiber payment attempts
- CKB commitment records and audit events

Database constraints reserve uniqueness for client event keys and one payout per accepted event. These constraints provide a durable backstop for service-level idempotency.

### Implemented Authorization and Site Scoping

Built session creation, lookup, expiry, revocation, role checks, and organization/site access controls.

Authorization now supports:

- Sponsor, operator, reviewer, administrator, and participant roles
- Organization-scoped sponsor access
- Site-scoped operator assignments
- Assignment revocation
- Cross-organization administration only for authorized administrators
- Rejection of expired or revoked sessions

An operator cannot submit a collection event for an organization or site outside the active assignment.

### Built Campaign Creation and Activation Rules

Implemented campaign drafts, canonical rule hashing, review, and guarded activation.

Campaign rules include:

- Organization and collection site
- Accepted material and measurement unit
- Reward rate and campaign budget
- Per-event and participant limits
- Campaign start and end times
- Review policy and consent version

Canonical serialization gives equivalent rules the same hash. Activation requires review approval and an active operator assignment, and activated rules cannot be silently changed.

### Added Participant Invitations, QR Identity, and Consent

Built the controlled participant onboarding path used at a collection site.

Participant handling includes:

- Single-use campaign invitations
- Invitation expiry and replay rejection
- Campaign-scoped signed QR identities
- QR expiry, tamper detection, and key rotation
- Participant suspension
- Versioned consent records
- Consent withdrawal enforcement

The QR payload is an application identity reference, not a wallet private key or Fiber payer credential.

### Implemented Collection Event Drafts and Intake

Added the operator workflow for recording an item count or weight and submitting the event for validation.

Event intake now provides:

- Draft creation and preview
- Operator, campaign, site, participant, material, quantity, and timestamp checks
- Optional private evidence references
- Client-generated idempotency keys
- Retry-safe submission that returns the existing event
- A stable handoff from received events to deterministic validation

Submitting an event does not immediately pay a reward. It creates a durable record that must still pass validation, review, and budget reservation.

### Verified the Foundation

Verification covered:

- Workspace configuration and secret-boundary tests
- Integer money and request-schema tests
- Database uniqueness and persistence checks
- Session, role, organization, and site authorization
- Campaign validation, canonical hashing, review, and activation
- Invitation, QR, suspension, and consent behavior
- Event draft, authorization, and idempotent submission behavior
- TypeScript type checks and production builds

These are deterministic local checks. No real recycling partner, Fiber payment, or CKB testnet commitment is claimed in this report.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-reloop-rewards | CKB and Fiber testnet recycling-incentive platform with scoped campaigns, signed participant QR identities, consent, operator authorization, and idempotent collection-event intake | [GitHub](https://github.com/Hallab7/ckb-reloop-rewards) |

## Key Concepts Learned

- Why a physical collection requires accountable operator verification rather than a software-only truth claim
- How organization and site scopes prevent operators from submitting events outside their assignments
- Why reward amounts and budgets should use integer base units
- How canonical campaign rules make later commitments and audits reproducible
- Why campaign activation should lock reviewed rules
- How signed, expiring QR identities reduce tampering without exposing wallet or payer secrets
- Why consent must be versioned and checked at the time of collection
- How database uniqueness and idempotency keys work together to make retries safe
- Why collection intake, validation, acceptance, and payment must remain separate state transitions
- Why local tests must be reported separately from partner, Fiber testnet, and CKB testnet evidence
