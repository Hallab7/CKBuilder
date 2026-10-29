# Week 21 - September 29-October 5 2026

Twenty-first week of the CKBuilder program. Started documenting **CKB Automata** by extending its testnet-only, non-custodial automation model with a constrained Nervos DAO harvest workflow.

This week focused on enforceable boundaries. The owner reviews and signs the setup transaction, contracts bind the principal and payout terms, approved executors can perform only the committed transitions, and owner recovery remains independent of the hosted application.

## What I Did

### Defined the DAO Harvest Safety Boundary

Documented the workflow as a CKB testnet pilot rather than a production yield product.

The threat model separates:

- CKB consensus and live cells as authoritative evidence
- Wallet-reviewed setup from background executor activity
- Owner funds from executor fee inputs
- Contract-enforced transitions from API and database projections
- Local and fixture evidence from public testnet acceptance
- Testnet experimentation from unsupported mainnet use

The scope excludes custody of owner private keys, arbitrary wallet transactions, mainnet deployment, and claims that an executor receipt can override chain state.

### Added the Canonical DAO Harvest Schema

Created a versioned Molecule schema and checked Rust and TypeScript representations for the harvest policy.

The immutable payload binds:

- Owner lock hash
- Compensation payout lock hash
- Harvest vault lock hash
- Nervos DAO type hash
- Exact principal capacity
- Approved prepare-executor set
- Executor reward
- Cycle limit and end bound
- Preparation-window controls

Cross-language fixtures keep SDK serialization aligned with what the CKB scripts decode.

### Implemented the Harvest Contracts

Added a Harvest Vault Lock and DAO Harvest Policy that work with the existing Job Lock.

The contracts constrain:

- Initial DAO deposit and automation creation
- Preparation of a deposited DAO cell
- Mature claim and exact-principal redeposit
- Exact compensation destination and executor reward
- Sequence and remaining-cycle progression
- One valid successor for continuing automation
- Owner stop, exit, and recovery paths

The protected principal cannot be treated as an executor fee source, and application services cannot authorize a different payout address after setup.

### Built DAO Math and Transaction Builders

Implemented exact integer helpers for CKB epoch fractions, DAO accumulated-rate calculations, occupied capacity, and maximum withdrawal values.

Added unsigned transaction builders for:

- Setup and initial deposit
- Preparation
- Claim and redeposit
- Owner stop
- Owner exit
- Mature owner recovery

Builders validate the network, deployment registry, live outpoints, owner and payout locks, principal, policy payload, and required outputs before returning a wallet-reviewable transaction.

### Added the Read Model, API, and Canonical Projection

Introduced database migrations and a dedicated DAO harvest projection instead of mixing harvest records into payment automation listings.

The API and indexer now support:

- Harvest job registration and lookup
- Public status and operation reads
- Unsigned setup and owner-action requests
- Canonical block provenance
- Consumption and successor tracking
- Reorganization rollback and replay
- Bounded projection queries
- Deployment identity checks

Database state remains a projection. Live CKB cells and canonical headers decide whether an action is still valid.

### Added Executor Scheduling and Recovery Operations

Built scheduler rules for preparation windows, DAO maturity, cycle bounds, reward budgets, executor authorization, contention, dropped transactions, and reorganization recovery.

Operational handling distinguishes:

- Retryable RPC and infrastructure outages
- Unsafe or stale transactions that must not be replayed
- Missed preparation windows that roll to a later DAO cycle
- Spent inputs and competing attempts
- Exhausted rewards and completed cycle limits
- Owner recovery that does not depend on Redis, the database, or an executor

Signed executor receipts are supporting records only. Confirmation must still be established from canonical CKB data.

### Built the Wallet-Reviewed User Experience

Added the DAO harvest setup form, review screen, dashboard, detail view, and owner recovery controls.

The wallet review compares the displayed intent with the serialized transaction, including:

- Principal
- Compensation address
- Cycle count
- Executor rewards and service charges
- Required contract outputs
- Owner-controlled recovery terms
- CKB testnet identity

Harvest records are presented separately from ordinary deadline and recurring payment automations.

### Published the Testnet Pilot Manifests

Added CKB testnet deployment manifests, contract metadata, public deployment checks, and a later corrected policy deployment within the week.

The manifests bind script code hashes, cell deps, network genesis, and deployment transactions. This proves that contract binaries were published to testnet; it does not by itself prove a complete setup, prepare, claim, redeposit, recovery, monitoring, or soak journey.

### Hardened the Integrated Journey

Added contract, SDK, API, executor, frontend, and browser coverage for the new workflow.

The repository checks include:

- Molecule payload compatibility
- Creation and mutation rejection
- Principal conservation
- Preparation and claim output rules
- Scheduler boundaries
- Reorganization behavior
- Recovery artifact generation
- Wallet review integrity
- Accessibility and product journeys

The internal security review is not an external audit and does not authorize mainnet use.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-automata | Testnet-only, non-custodial CKB automation platform extended with constrained Nervos DAO harvest contracts, deterministic transaction builders, canonical indexing, executor scheduling, owner recovery, and wallet-reviewed setup | [GitHub](https://github.com/Hallab7/ckb-automata) |

## Key Concepts Learned

- Why a hosted automation service should never become the authority for chain state
- How immutable Molecule payloads bind owner intent across Rust contracts and TypeScript builders
- Why DAO principal, executor rewards, and transaction fees need separate accounting boundaries
- How exact CKB epoch fractions avoid unsafe calendar-time assumptions
- Why an approved executor still needs contract limits on every output and successor
- How consumed-cell uniqueness prevents two competing transactions from both becoming canonical
- Why database rows and signed receipts are evidence aids rather than settlement truth
- How an owner-only recovery path reduces dependence on API, Redis, database, and executor availability
- Why a deployed contract manifest is not evidence of a completed live workflow
- Why testnet-only labeling and an external-audit boundary are part of the technical design
