# Week 10 - July 7-13 2026

Tenth week of the CKBuilder program. Started building **FiberSLA** - open-source payment reliability and observability infrastructure for the CKB Fiber Network.

This week focused on validating the monitoring problem, establishing the project foundation, building a version-aware Fiber RPC boundary, and implementing passive and guarded active probes.

## What I Did

### Created the FiberSLA Validation Workflow

Built a two-node testnet validation workflow for checking real Fiber payment readiness.

The validation tooling supports:

- Sender and receiver Fiber RPC configuration
- Node information and compatibility checks
- Invoice creation, parsing, retrieval, and cancellation
- Route dry-run checks
- Optional controlled testnet payment submission
- Payment and invoice settlement reconciliation
- Sanitized JSON evidence generation
- Controlled RPC failure injection

Captured sanitized fixtures for the pinned Fiber compatibility target and documented which results were verified through fixtures and which still require funded live nodes.

### Established the TypeScript Monorepo

Created a pnpm and TypeScript workspace with packages for:

- Domain contracts
- Fiber RPC compatibility
- Storage
- Observability
- Probe execution
- Agent service

Added shared configuration for TypeScript, ESLint, Prettier, Vitest, GitHub Actions, environment variables, licensing, contribution guidelines, and security reporting.

Documented the system architecture, threat model, data handling rules, RPC compatibility policy, and active-probe safety controls.

### Implemented Durable Contracts and Storage

Created runtime-validated contracts for:

- Monitors
- Probe definitions
- Probe runs
- Observations
- Supported assets
- Active-probe safety policies
- Spend reservations

Implemented SQLite and PostgreSQL storage adapters behind the same repository contract.

The storage layer now provides:

- Duplicate scheduled-run prevention
- Durable observations
- Transactional spend reservations
- Per-payment, hourly, and daily limits
- Destination, network, and asset allowlists
- Minimum probe intervals
- Persisted emergency-stop state
- Unresolved ambiguous-payment locks

All monetary values are stored as base-unit integer strings instead of floating-point values.

### Built the Version-Aware Fiber RPC Layer

Implemented a private server-side JSON-RPC adapter for the pinned Fiber release.

The adapter supports:

- Node information
- Peer and channel lists
- Invoice creation, parsing, retrieval, and cancellation
- Route dry-runs
- Payment submission and reconciliation
- Authentication headers
- Request timeouts
- Response-size limits
- Safe retry rules for read operations
- Normalized and sanitized errors
- Strict or advisory version compatibility

Payment mutations are never blindly retried after an ambiguous response.

### Added Passive Readiness Monitoring

Implemented five passive probe types:

- RPC availability
- Node synchronization evidence
- Peer connectivity
- Channel readiness
- Asset readiness

Added a durable scheduler with interval-boundary deduplication and timeout handling.

Built a local Fastify agent exposing:

- Health and readiness checks
- Monitor status
- Probe observations
- Manual probe execution
- Prometheus metrics
- Persisted emergency stop

### Added Guarded Active Probes

Implemented controlled receiver checks for:

- Invoice round trips
- Route dry-runs
- Invoice payment canaries
- Keysend canaries

The payment canaries enforce persisted safety policy immediately before submission. An ambiguous invoice payment is reconciled against both sender payment state and receiver invoice state. If the result cannot be proven, later spending is locked.

Active probes remain disabled and emergency-stopped by default.

### Verified the Build

Ran:

```bash
pnpm format:check
pnpm lint
pnpm typecheck
pnpm test
pnpm build
pnpm validate:failure
pnpm validate:fixtures
docker compose -f infra/docker-compose.yml config
```

Verification results:

- 19 automated tests passed
- ESLint passed
- TypeScript type checking passed
- Production build completed successfully
- Controlled RPC failure was detected correctly
- Sanitized RPC fixtures validated successfully
- Docker Compose configuration validated successfully

The live two-node payment was not executed because funded Fiber nodes were not available during this work. Active payment functionality therefore remains explicitly testnet-only, disabled by default, and unclaimed as live settlement evidence.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| fiber-sla | Open-source CKB Fiber payment reliability infrastructure with passive monitoring, guarded payment canaries, durable evidence, metrics, and safety controls | [GitHub](https://github.com/Hallab7/ckb-fiber-sla) |

## Key Concepts Learned

- How to model payment readiness as evidence rather than node uptime
- How to keep privileged Fiber RPC access strictly server-side
- How to isolate unstable RPC shapes behind a version-aware adapter
- Why active payment intent must be persisted before sending funds
- How transactional spending limits prevent concurrent budget races
- Why ambiguous payment submissions must be reconciled instead of retried
- How to distinguish passive evidence, route simulations, and settled-payment proof
- Why unknown is safer than claiming readiness without sufficient directional evidence
- How fixture compatibility differs from a funded live testnet verification
- How safe defaults protect operators while a monitoring system is still unaudited
