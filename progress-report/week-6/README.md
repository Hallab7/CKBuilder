# Week 6 - June 7-14 2026

Sixth week of the CKBuilder program. Started the capstone direction with **FiberSave** - a non-custodial savings and remittance application built around CKB, RGB++ assets, and the Fiber Network.

This week focused on turning the idea into a fundable product spec and then implementing the first four MVP phases: project foundation, wallet/balance shell, savings goals, deposits, withdrawals, activity, and settings.

## What I Did

### Designed the Project

Picked **FiberSave** as the next major project. The goal is to build a consumer-facing CKB application for users who need practical savings and low-cost remittance tools.

The product direction:

- Personal savings app first
- Fiber remittance tool second
- Group savings circles later

Key design decisions:

- Non-custodial by default - users keep control of funds through their wallet
- Savings goals are metadata in the MVP, not locked funds
- CKB testnet is the first target network
- RGB++ stable assets are part of the roadmap, with CKB active first
- Fiber remittance and savings circles are deferred until the base wallet/savings flows are stable

### Wrote Product and Funding Specs

Created multiple planning documents for FiberSave:

- Original design outline
- Revised design outline with tighter MVP scope
- Funding submission design spec
- Phase-by-phase implementation plan

The funding spec frames FiberSave as a practical consumer use case for CKB/Fiber infrastructure, especially for emerging markets where informal savings groups and expensive remittance providers are common.

### Implemented Phase 1 - Project Foundation

Set up the project foundation:

- Created a Next.js + TypeScript + Tailwind frontend
- Installed CCC wallet packages
- Added `lucide-react` for UI icons
- Added project docs for product decisions and architecture
- Added CKB testnet environment configuration
- Added shared FiberSave TypeScript types
- Added the initial app shell

Core files:

- `docs/architecture.md`
- `docs/product-decisions.md`
- `frontend/src/types/fibersave.ts`
- `frontend/src/app/providers.tsx`

### Implemented Phase 2 - Wallet and Balances

Built the first wallet and balance layer:

- CCC provider integration
- Wallet connect/disconnect button
- Wallet utility module
- Balance query module
- CKB balance formatting from shannons
- RGB++ stable asset placeholder behind a stable balance interface
- Phase 2 dashboard with wallet state and balances

Core files:

- `frontend/src/lib/wallet.ts`
- `frontend/src/lib/balances.ts`
- `frontend/src/lib/format.ts`
- `frontend/src/components/connect-wallet-button.tsx`
- `frontend/src/components/balance-card.tsx`
- `frontend/src/components/fibersave-dashboard.tsx`

### Implemented Phase 3 - Savings Goals

Built the savings goal MVP using local browser metadata:

- `/goals` - list savings goals
- `/goals/new` - create a new goal
- `/goals/[goalId]` - view and update a goal
- Local goal storage keyed by wallet address
- Goal assignment and removal logic
- Goal progress calculation
- Goal creation, assignment, withdrawal, completion, and archive states

Important MVP rule:

Goal assignment updates metadata only. It does not lock, transfer, or spend funds. This keeps the product non-custodial and simple for the first build.

### Implemented Phase 4 - Deposits, Withdrawals, Activity, Settings

Added the core user workflow pages:

- `/deposit`
- `/withdraw`
- `/activity`
- `/settings`

Deposit flow:

- Shows connected wallet address
- Generates a QR code locally
- Supports copy-to-clipboard
- Shows supported assets
- Records deposit check activity

Withdrawal flow:

- Validates recipient CKB address with CCC
- Validates amount input
- Records a pending withdrawal activity item
- Leaves real transaction construction/signing for the testnet integration phase

Activity flow:

- Local activity store
- Filters for all, deposits, withdrawals, goals, and pending
- Goal creation and goal assignment now write activity records

Settings flow:

- Connected wallet display
- Preferred display currency selector
- Security boundary explanation
- Supported asset notes
- Clear local metadata during development

### Verified the Build

Ran:

```bash
pnpm lint
pnpm build
```

The build completed successfully and generated the expected routes:

- `/`
- `/goals`
- `/goals/new`
- `/goals/[goalId]`
- `/deposit`
- `/withdraw`
- `/activity`
- `/settings`

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-fiber-save | Non-custodial savings and remittance app built around CKB, RGB++ assets, and Fiber | [GitHub](https://github.com/Hallab7/ckb-fiber-save) |

## Key Concepts Learned

- How to scope a CKB/Fiber consumer app into realistic implementation phases
- Why savings goals should start as metadata before introducing on-chain locking
- How to keep a clear non-custodial boundary in product and architecture
- How to integrate CCC wallet provider and signer hooks into a Next.js app
- How to model balances, goals, and activity as replaceable modules
- How to use local storage as a prototype metadata layer without blocking a later backend
- How to separate Fiber remittance from the base savings MVP to reduce delivery risk
- How to write a funding-oriented design spec with milestones, risks, and ecosystem value


Important remaining work:

- Add automated tests for goal storage, goal actions, activity store, and balance helpers
- Add UI flow tests
- Improve mobile polish
- Wire real CKB withdrawal transaction construction and signing
- Prepare the demo script for funding/ecosystem review
