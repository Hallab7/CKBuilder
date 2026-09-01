# Week 15 - August 16-23 2026

Fifteenth week of the CKBuilder program. Started building **SignalBounty** - a CKB and Fiber testnet marketplace for sponsored mobile-network measurement campaigns where invited Android contributors submit foreground signal and latency samples for RUSD rewards.

This week focused on turning the product brief into a working repository foundation, sponsor campaign flow, Android collection scaffold, deterministic validation engine, and local evidence boundary.

## What I Did

### Established the SignalBounty Repository Foundation

Created a pnpm and TypeScript workspace for the beta implementation.

The repository foundation includes:

- Fastify API application
- Next.js sponsor dashboard
- Worker application
- Android app scaffold
- Shared contracts package
- In-memory development store
- Prisma schema for durable storage
- CKB commitment boundary
- Fiber RPC boundary
- Geospatial validation helpers
- Vitest coverage and production build scripts
- Docker Compose services for PostgreSQL and Redis

The goal was to make the project testable locally before depending on funded Fiber nodes, a CKB signer, or a hosted pilot environment.

### Defined Shared Campaign and Money Contracts

Built the shared domain layer used by the API, worker, dashboard, and future Android integration.

The contracts cover:

- Sponsor campaign rules
- Campaign area and coarse public grid policy
- Measurement submission shape
- Validation decision states
- Accepted measurement records
- Sample payout records
- Dataset commitment records
- RUSD parsing, formatting, and integer base-unit arithmetic
- Canonical JSON hashing for rules and audit evidence

The money helpers keep RUSD values as integer base-unit strings so payout calculations do not depend on floating-point values.

### Built Sponsor Campaign Creation and Review

Implemented the sponsor organization and campaign workflow in the API.

The sponsor-side API now supports:

- Reviewed sponsor organization creation
- Campaign draft creation
- Campaign rules canonicalization
- Rules hash generation
- Submission for review
- Approval into an active or scheduled campaign
- Campaign pause handling
- Campaign listing and detail lookup

The sponsor dashboard shows the pilot campaign, collection progress, accepted and rejected counts, budget, paid amount, rules hash, and CKB commitment placeholder.

### Added Android Foreground Measurement Scaffold

Created the Android project structure with Kotlin models, manifest permissions, and local test files.

The Android scaffold includes:

- User-initiated measurement capture boundary
- No hidden background collection
- Mobile network type and carrier label fields
- Radio signal metrics where available
- Latency measurement model
- Coarse public grid location model
- Local submission queue abstraction
- Unit-test files for collector and queue behavior

The app does not use stable Android hardware identifiers for contributor identity.

### Implemented Submission Intake and Deterministic Validation

Added the API route for measurement uploads and the validation engine used to classify submissions.

Validation checks include:

- Campaign status
- Required metric presence
- Measurement time window
- Submission delay
- Allowed public grid cell
- Location accuracy threshold
- Duplicate evidence hash
- Campaign sample cap
- Contributor sample cap
- Grid-cell sample cap
- Latency target mismatch
- Implausible signal values
- High latency outliers routed to review

Accepted samples receive a stable export row hash and are recorded as payable accepted measurements. Rejected samples keep non-sensitive reason codes.

### Added Worker Validation

Built a worker-side validation job that reuses the same deterministic engine as the API.

The worker records:

- Accepted decisions
- Rejected decisions
- Review-required decisions
- Export row hashes for accepted measurements
- Repeatable reason codes for failed checks

This keeps validation behavior centralized instead of splitting rules across routes and jobs.

### Verified the Local Foundation

Local verification covered:

- Contract tests
- Money tests
- Campaign API tests
- Validation-engine tests
- Worker validation tests
- Prisma schema validation
- TypeScript package build
- Next.js production build
- Mock Android, Fiber, and CKB evidence validation

Android Gradle tests were not run because this machine does not have Gradle, adb, or the Android SDK configured. Live Fiber settlement and live CKB publication were not claimed because funded testnet infrastructure and signer configuration were not available.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-signalbounty | CKB and Fiber testnet mobile-network measurement marketplace with sponsor campaigns, Android foreground collection, deterministic validation, and privacy-conscious evidence handling | [GitHub](https://github.com/Hallab7/ckb-signalbounty) |

## Key Concepts Learned

- Why campaign rules need canonical hashes before collection begins
- How coarse public grid cells preserve sponsor utility without exporting exact paths
- Why Android measurement collection should require visible user action
- How validation reason codes make rejected samples explainable without leaking private evidence
- Why sample caps need campaign, contributor, and grid-cell boundaries
- How a shared validation engine prevents API and worker behavior from drifting
- Why RUSD amounts should remain integer base-unit strings
- How mock Fiber and CKB evidence can support development without pretending to be live-network evidence
- Why CKB commitments anchor published hashes but do not prove that a radio measurement is physically true
- How a narrow pilot scope keeps privacy, abuse handling, and verification practical
