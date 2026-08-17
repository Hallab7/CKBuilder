# Week 13 - July 28-August 3 2026

Thirteenth week of the CKBuilder program. Started building **SkillMinute** - a CKB and Fiber testnet application for short expert-help sessions with fixed five-minute billing intervals and a learner-authorized spending cap.

This week focused on validating the payment workflow, establishing the repository and storage foundation, implementing secure identities and reviewed expert profiles, building collision-safe booking, and enforcing immutable session authorization.

## What I Did

### Created the Fiber Interval Validation Workflow

Built an explicit mock and live validation harness for the intended two-node Fiber settlement flow.

The validation tooling covers:

- Payer and receiver node information
- Fiber version compatibility
- RUSD asset configuration
- Exact receiver invoice creation
- Invoice parsing and validation
- Route and fee dry-runs
- Payment submission
- Sender and receiver reconciliation
- Three consecutive interval payments
- Rejection of a fourth interval beyond a three-interval cap
- Duplicate-request protection
- Receiver failure behavior
- Sanitized JSON evidence

The mock workflow was validated with deterministic fixtures. A real testnet transfer was not claimed because funded payer and receiver nodes were not available.

### Established the SkillMinute Monorepo

Created a pnpm and TypeScript workspace containing:

- Next.js web application
- Background worker
- Shared domain contracts
- Application services
- PostgreSQL repository adapters
- Fiber RPC boundary
- CKB commitment package

Added shared TypeScript configuration, Vitest testing, continuous checks, environment examples, local Docker services, and production build commands.

The local infrastructure includes PostgreSQL, Redis, and LiveKit. Database initialization includes a baseline migration and pilot seed records with safe placeholder identities.

### Implemented One-Time Identity Authentication

Built a wallet identity flow around canonical, short-lived challenges.

Authentication now provides:

- Random one-time nonces
- Hashed challenge storage
- Expiration checks
- Replay prevention
- CCC signature verification
- Signed HTTP-only identity sessions
- Production-only session-secret enforcement
- A pilot magic-link fallback boundary

The authenticated user is derived from the signed session cookie rather than accepting a user ID from a browser request.

### Built Reviewed Expert Profiles

Implemented learner and expert profile services with explicit beta consent.

Expert onboarding includes:

- Headline and private biography
- Skill list
- Five-minute interval price
- Receiver mode and optional receiver public key
- Draft and review states
- Administrator approval, rejection, and suspension
- Audit events for review decisions

Changing a reviewed price or settlement destination returns the expert profile to manual review. Only approved experts appear in the public directory or create availability.

### Added Availability and Collision-Safe Booking

Built timezone-aware availability management and capped learner booking.

The booking workflow now enforces:

- UTC storage with an explicit display timezone
- Start and end validation
- Overlap rejection
- Approved-expert access
- Immutable price and interval snapshots
- Learner-selected maximum interval count
- Calculated maximum total in integer RUSD base units
- Serializable slot claims
- One booking per availability slot
- Atomic session creation
- Durable learner and expert notification records

Concurrent requests for the same slot produce one durable winner instead of duplicate bookings.

### Implemented Canonical Session Authorization

Created stable authorization terms containing the booking, participants, network, rate, interval duration, maximum interval count, maximum total, start time, expiry, and one-time nonce.

The learner must sign the exact terms hash. Authorization is rejected when:

- The terms have changed
- The maximum-total calculation is inconsistent
- The request has expired
- The signer is not the booked learner
- The session is already authorized
- Billing has already started

This prevents a server or later profile edit from silently changing the learner's authorized spending limit.

### Built the Guarded Session State Engine

Implemented optimistic, versioned session commands for waiting, introduction, authorization, interval requests, payment confirmation, presence pauses, payment pauses, cap completion, and session ending.

The engine rejects:

- Invalid state transitions
- Stale command versions
- Paid time without confirmed payment
- A new interval without authorization
- A payment beyond the signed interval count
- Presence recovery without server confirmation
- Direct route-level state mutation

Added a complete allowed and forbidden transition matrix so every state edge is tested.

### Verified the Foundation

Verification results at the end of the week:

- 151 automated tests passed
- TypeScript checks passed
- Prisma schema validation passed
- Production application build passed
- Mock Fiber interval and failure fixtures passed
- Authorization mutation and cap-overflow tests passed
- Booking collision and state-transition tests passed

Live Fiber settlement remained an explicit external evidence gate requiring funded testnet nodes and compatible RUSD liquidity.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-skillminute | CKB and Fiber testnet expert-help application with reviewed experts, capped booking, signed session authorization, and guarded interval billing | [GitHub](https://github.com/Hallab7/ckb-skillminute) |

## Key Concepts Learned

- How to model interval billing without describing it as continuous per-second settlement
- Why mock Fiber evidence must remain separate from funded testnet evidence
- How canonical one-time challenges prevent signature replay
- Why authenticated identity should come from a protected session instead of request data
- How manual expert review protects settlement destinations and pricing
- Why booking prices must be immutable snapshots
- How serializable slot claims prevent concurrent double booking
- Why money must remain integer base-unit strings throughout the application
- How a signed maximum interval count creates an enforceable spending boundary
- Why state changes need optimistic versions and centralized transition guards
- How durable notification records avoid losing booking confirmations
