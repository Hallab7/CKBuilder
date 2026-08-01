# Week 12 - July 21-27 2026

Twelfth week of the CKBuilder program. Completed the repository implementation for **FiberSLA** by adding reproducible deployments, public-beta operations, mainnet-safe defaults, security and release automation, and a transparent DAO handover package.

This week focused on turning the working application into infrastructure that operators can deploy, recover, verify, and maintain independently.

## What I Did

### Added Reproducible Deployment Infrastructure

Expanded the infrastructure from a single local agent into documented local and coordinated deployments.

The local stack now includes:

- FiberSLA agent
- Persistent SQLite volume
- Prometheus metrics collection
- Optional Grafana service
- Loopback-only published ports
- Container health checks

The coordinated stack now includes:

- PostgreSQL
- FiberSLA coordinator
- Independent worker agent
- Next.js operator dashboard
- Nginx reverse proxy
- Dependency-aware health checks
- Private internal service networking

The coordinator can now select PostgreSQL through `DATABASE_URL` while retaining SQLite for simple local operation.

### Implemented Backup, Restore, and Upgrade Procedures

Added PowerShell tooling for safe SQLite backup and restoration. The restore command refuses to overwrite an existing database.

Documented:

- Consistent SQLite backup procedure
- PostgreSQL logical backup with `pg_dump`
- Restoration into a clean PostgreSQL database
- Signing-key and configuration recovery
- Migration preflight
- Container rollback procedure
- Forward-only handling for destructive migrations
- Post-upgrade health and ingestion checks

Created a deployment preflight command that checks required secrets, configuration files, and whether the Fiber RPC endpoint uses a local or private-network address.

Both local and coordinated Compose configurations were validated successfully.

### Created the Public Beta Trial Workflow

Added an operator onboarding and feedback process for real testnet trials.

The workflow includes:

- Sanitized operator trial template
- Consent-controlled JSON evidence records
- Public GitHub beta-finding form
- Private security-reporting link
- Severity and resolution register
- Privacy checklist for submitted findings
- Automated evidence validation

The evidence gate requires three completed and consented trials from distinct operators across multiple environments. It also rejects completion while any critical or high-severity finding remains open.

No external trial, hosted deployment, or public status URL is claimed until real evidence is supplied.

### Hardened Mainnet-Compatible Operation

Added a passive-only mainnet configuration profile.

Mainnet safeguards now enforce:

- Localhost API binding
- Strict Fiber version compatibility
- Active probes disabled by default
- Emergency stop enabled by default
- Empty target allowlist
- No configured receiver RPC
- Private monitoring by default

Attempting to relax strict version enforcement or bind the agent publicly on mainnet now fails during startup.

Mainnet payment canaries require a separate exact acknowledgment in addition to every existing destination, asset, amount, fee, interval, hourly budget, daily budget, and emergency-stop control.

Added automated tests for these mainnet protections.

### Added Security and Release Automation

Created GitHub workflows for dependency review, filesystem scanning, prerelease verification, container publication, SBOM generation, source archives, and SHA-256 checksums.

The release workflow builds separate images for:

- Agent
- Coordinator
- Dashboard

The workflow cannot publish a beta release until external trial evidence passes.

During verification, the dependency audit identified advisories affecting Fastify and transitive Sharp, PostCSS, and brace-expansion packages. Upgraded or pinned patched versions and reran the audit successfully with no known vulnerabilities.

### Completed the DAO Handover Package

Added documentation needed for independent verification and continued maintenance:

- Deployment and recovery guide
- Operations incident runbook
- API and metrics specification
- Mainnet-compatible beta guide
- Maintenance and sustainability plan
- Transparent budget report
- Known limitations
- Claim-by-claim handover report
- Reproducible release-verification command

The operations runbook covers Fiber RPC outages, authentication failures, unsupported versions, scheduler backlog, unexpected probe spending, ambiguous payments, coordinator downtime, signature failures, alert-provider outages, PostgreSQL storage exhaustion, emergency stop activation, compromised agents, key rotation, and backup restoration.

The budget report records the planned allocation while clearly stating that no disbursement transaction, exchange-rate rule, or expense receipts were supplied. It does not invent spending claims.

### Verified the Complete Repository

Created one command that runs the full release verification suite and writes structured local evidence.

Verification results:

- 49 unit tests passed
- 7 integration tests passed
- 3 Playwright browser tests passed
- Formatting passed
- ESLint passed
- TypeScript type checking passed
- All workspace packages built successfully
- Next.js production build completed successfully
- Dependency audit reported no known vulnerabilities
- Local and coordinated Compose files passed configuration validation

The repository implementation is complete. Live testnet payment evidence, a verified UDT route, external operator trials, public hosting, demo video, final tag publication, and actual DAO spending remain explicit evidence gates requiring real infrastructure or owner-supplied records.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| fiber-sla | Deployment-ready CKB Fiber reliability platform with recovery tooling, public-beta operations, mainnet-safe defaults, security automation, and DAO handover documentation | [GitHub](https://github.com/Hallab7/ckb-fiber-sla) |

## Key Concepts Learned

- How to separate repository completion from external operational evidence
- Why deployment health checks should reflect service dependencies
- How SQLite and PostgreSQL recovery procedures differ
- Why destructive database migrations require forward recovery instead of blind rollback
- How evidence gates prevent incomplete beta claims from being published
- Why external trial records need consent and aggressive sanitization
- How passive-only mainnet defaults reduce financial risk
- Why a spending acknowledgment must supplement rather than replace safety controls
- How automated dependency audits can expose release-blocking vulnerabilities
- How SBOMs and checksums make release artifacts independently verifiable
- Why operational runbooks are part of reliability infrastructure
- How transparent DAO reporting should distinguish planned budgets from actual spending
