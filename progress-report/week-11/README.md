# Week 11 - July 14-20 2026

Eleventh week of the CKBuilder program. Continued building **FiberSLA** by turning raw probe evidence into diagnoses, incidents, alerts, multi-agent coordination, operator dashboards, and integration tooling.

This week focused on making Fiber readiness evidence actionable for operators and consumable by wallets and merchant applications.

## What I Did

### Built the Deterministic Diagnosis Engine

Created a stable failure taxonomy and an ordered rule-based classifier.

The diagnosis engine now classifies:

- RPC connection failures
- RPC authorization failures
- Unsupported Fiber versions
- Unsynchronized nodes
- Missing peers
- Non-ready channels
- Insufficient outbound liquidity
- Insufficient inbound capacity
- Unsupported assets
- Missing asset routes
- Fee-limit failures
- Invalid or expired invoices
- Payment timeouts
- Payment rejection
- Exhausted probe budgets
- Internal probe failures
- Unknown payment failures

Each diagnosis includes severity, confidence, evidence codes, a concise summary, recommended operator actions, and a documentation link.

Unknown remains an intentional result when the available evidence cannot support a more specific conclusion.

### Added Durable Incident Management

Implemented an incident state machine backed by both SQLite and PostgreSQL.

Incident handling now supports:

- Configurable failure thresholds
- Immediate opening for critical failures
- Deduplication by probe and failure category
- Acknowledgement
- Recovery counting
- Configurable resolution thresholds
- Immutable incident event history
- Restart-safe incident state

Recovery observations do not immediately close an incident. The configured number of consecutive successful observations must be reached first.

### Implemented Restart-Safe Operator Alerts

Built durable alert evaluation and delivery infrastructure.

The alerting system supports:

- Incident open and resolution notifications
- Rule cooldowns
- Duplicate suppression
- HMAC-SHA256 signed webhooks
- Exponential retry
- Dead-letter state
- Manual retry
- Redacted provider responses
- Secret references instead of stored plaintext secrets

Added delivery adapters for:

- Generic HTTPS webhooks
- HTTPS email relays
- Discord webhooks
- Telegram gateways

Private-network webhook destinations are blocked by default to reduce server-side request forgery risk.

### Added Multi-Agent Coordination

Built a coordinator service for receiving evidence from independent FiberSLA agents.

The coordinator now provides:

- Short-lived one-time enrollment tokens
- Agent-generated Ed25519 signing keys
- Signed observation ingestion
- Timestamp and clock-skew validation
- Nonce replay protection
- Batch-size limits
- Observation deduplication
- Agent heartbeats
- Agent and Fiber version tracking
- Scheduler-lag tracking
- Remote schedule distribution
- Opt-in public aggregation

Local agents remain operational when the coordinator is unavailable. Added a SQLite-backed upload queue that retains observation batches across restarts and uploads them after connectivity returns.

Forged and replayed observation batches are rejected.

### Built the Operator Dashboard

Created an authenticated Next.js and Tailwind CSS dashboard.

The dashboard includes:

- Network overview
- Monitor list and detail views
- Latest probe evidence
- Diagnosis and recommendations
- Incident history
- Alert delivery state
- Agent health
- Asset verification states
- Fiber compatibility status
- Public network aggregates
- Per-monitor public status pages

Passive checks, route simulations, and settled-payment evidence are labeled separately.

The public pages only expose monitors that explicitly opt in. They exclude node references, peer identities, channel details, routes, payment hashes, and raw private evidence.

Added secure session cookies, protected operator routes, content security policy, origin-safe redirects, and browser privacy tests.

### Added the Readiness API and TypeScript SDK

Implemented a readiness endpoint on both the local agent and coordinator:

```text
GET /v1/readiness?monitor_id=...&asset=ckb&direction=receive&amount=...
```

The response includes:

- Ready, degraded, not-ready, or unknown state
- Fresh, stale, or missing evidence state
- Evidence types
- Confidence
- Active incidents
- Recommended action
- Latest observation timestamp

Created `@fiber-sla/sdk` with:

- Typed readiness requests and responses
- Runtime response validation
- Request timeout handling
- Server-side checkout guard
- Explicit stale-evidence handling
- Wallet readiness example
- Merchant checkout middleware example

The checkout guard fails closed unless readiness evidence is both fresh and ready. The SDK only talks to the FiberSLA HTTP API and never exposes Fiber RPC access to browser code.

### Verified the Build

Ran:

```bash
pnpm format:check
pnpm lint
pnpm typecheck
pnpm test
pnpm test:integration
pnpm test:e2e
pnpm build
```

Verification results:

- 45 unit tests passed
- 7 integration tests passed
- 3 Playwright browser tests passed
- ESLint passed
- TypeScript type checking passed
- Next.js production build completed successfully
- All workspace packages built successfully
- Signed ingestion, replay rejection, offline queue recovery, alert retries, stale readiness, authentication, and public-page privacy were tested

The production dependency audit could not complete because the package registry returned malformed compressed responses on repeated attempts. This remains an external verification item to rerun when the registry endpoint is healthy.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| fiber-sla | CKB Fiber reliability platform with deterministic diagnoses, durable incidents and alerts, signed multi-agent coordination, operator dashboard, readiness API, and integration SDK | [GitHub](https://github.com/Hallab7/ckb-fiber-sla) |

## Key Concepts Learned

- How to classify operational failures deterministically from structured evidence
- Why unknown evidence must remain separate from failure and readiness
- How failure and recovery thresholds reduce incident flapping
- How durable delivery queues prevent alerts from disappearing after restarts
- How to sign webhook payloads and observation batches
- How nonce tracking and timestamp validation prevent replay attacks
- Why agent-generated signing keys reduce coordinator trust
- How local safety authority can coexist with remote scheduling
- How to publish useful network statistics without exposing private infrastructure
- How to make passive, simulated, and settled-payment evidence visually distinct
- Why merchant checkout should fail closed when readiness data is stale
- How wallets and merchants can consume readiness without receiving Fiber RPC credentials
