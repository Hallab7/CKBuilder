# Week 9 - June 30-July 6 2026

Ninth week of the CKBuilder program. Continued the Fiber infrastructure direction by building **Fiber Merchant SDK** - a reusable merchant payment infrastructure SDK for CKB Fiber Network.

This week focused on turning the FiberSave remittance learnings into reusable infrastructure that other merchants, wallets, and Fiber payment applications can integrate.

## What I Did

### Created the Fiber Merchant SDK Monorepo

Started a new TypeScript monorepo for reusable Fiber merchant infrastructure.

The workspace now includes:

- `@fiber-merchant/core`
- `@fiber-merchant/node-client`
- `@fiber-merchant/service`
- `@fiber-merchant/webhook`
- `@fiber-merchant/react`
- `apps/merchant-demo`
- `apps/webhook-receiver`

The project is structured as infrastructure rather than a single merchant product. The goal is to provide reusable pieces for payment readiness, invoice creation, settlement tracking, signed webhooks, and browser-safe checkout flows.

### Implemented Core Payment Infrastructure

Built the core SDK layers needed for a Fiber merchant integration.

The implemented core modules now support:

- Exact CKB amount parsing and formatting
- Native CKB asset registration
- Guarded UDT verification path
- Persistent invoice and event storage
- Idempotent payment intent creation
- Restart-safe settlement monitoring
- Evidence-based payment readiness diagnostics
- Structured logs, metrics, health checks, and correlation IDs

The persistence layer uses SQLite for the hackathon prototype and keeps repository contracts separate from the storage implementation so it can later be replaced with a production database adapter.

### Built the Typed Fiber RPC Client

Implemented a server-side JSON-RPC client for the pinned Fiber compatibility target.

The client includes:

- JSON-RPC request construction
- Request timeout and abort support
- Authentication header support
- Safe retry handling
- Response size limits
- Typed error mapping
- Runtime response validation
- Sanitized error details

Captured fixtures and compatibility documentation are pinned to Fiber `v0.9.0-rc5`.

### Added Merchant API and Webhook Infrastructure

Built a framework-neutral merchant HTTP API and durable webhook layer.

The service package supports:

- `POST /v1/payment-readiness`
- `POST /v1/payment-intents`
- `GET /v1/payment-intents/:paymentIntentId`
- `POST /v1/payment-intents/:paymentIntentId/cancel`
- `GET /health/live`
- `GET /health/ready`
- `GET /metrics`

The webhook package supports:

- HMAC-SHA256 event signatures
- Raw-body signature verification
- Timestamp tolerance
- Secret rotation
- Durable delivery retries
- Dead-letter handling
- Local receiver example with duplicate event protection

This makes merchant fulfillment depend on signed server-side events instead of browser callbacks.

### Added React Checkout and Demo Apps

Created a browser-safe React checkout package and reference demo apps.

The checkout component:

- Shows Fiber invoice details
- Supports copy-to-clipboard
- Polls only the merchant backend
- Supports light, dark, and auto themes
- Does not expose Fiber node RPC URLs or credentials

The demo apps show:

- Merchant payment intent creation
- Readiness checks
- Webhook signature verification
- Duplicate-safe webhook receipt
- Deterministic full-flow recovery testing

### Prepared the Hackathon Release Package

Prepared the SDK for a hackathon beta release.

Added documentation for:

- Quick start
- Architecture
- Payment readiness
- Webhook integration
- OpenAPI
- Security review
- Observability
- Demo guide
- Production limitations
- UDT verification
- Hackathon submission draft
- Demo video script
- Submission audit checklist

Updated all SDK packages to `0.1.0-beta.1`, added MIT license metadata, enabled npm provenance metadata, added package dry-run checks, and excluded compiled test files from release tarballs.

### Verified the Build

Ran:

```bash
pnpm format:check
pnpm lint
pnpm typecheck
pnpm test
pnpm build
pnpm test:integration
pnpm release:check
pnpm release:pack
pnpm security:audit
docker compose -f infra/docker-compose.yml config --quiet
```

Verification results:

- 60 unit tests passed
- 4 integration tests passed
- ESLint passed
- TypeScript type checking passed
- Production build completed successfully
- Release check passed for 5 SDK packages
- Package dry-run completed for all SDK packages
- Security audit passed with no known vulnerabilities after upgrading Vitest
- Docker Compose configuration validated successfully

The SDK packages prepared for beta release:

- `@fiber-merchant/core`
- `@fiber-merchant/node-client`
- `@fiber-merchant/service`
- `@fiber-merchant/webhook`
- `@fiber-merchant/react`

## Projects

| Project | Description | Link |
|---------|-------------|------|
| fiber-merchant-sdk | Reusable Fiber merchant infrastructure SDK with readiness diagnostics, invoice service, settlement monitoring, signed webhooks, React checkout, and hackathon submission docs | [GitHub](https://github.com/Hallab7/fiber-merchant-sdk) |

## Key Concepts Learned

- How to turn a Fiber application feature into reusable infrastructure
- How to keep Fiber node RPC access strictly server-side
- How to model merchant payment state as invoices, events, deliveries, and idempotency keys
- How to make settlement recovery restart-safe
- How to use signed webhooks as the merchant fulfillment boundary
- How to distinguish deterministic integration tests from live funded Fiber payment evidence
- How to prepare npm packages for beta release without publishing them
- Why package dry-runs are important for catching unwanted release artifacts
- Why UDT support should remain evidence-gated until a real Fiber UDT payment succeeds
