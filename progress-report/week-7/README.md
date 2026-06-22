# Week 7 - June 15-21 2026

Seventh week of the CKBuilder program. Continued building **FiberSave** by completing the native CKB testnet transaction flow and preparing the personal savings MVP for a controlled ecosystem demo.

This week focused on completing Phase 5: automated testing, demo preparation, and documenting the remaining path toward Fiber remittance.

## What I Did

### Completed Native CKB Transactions

Finished the deposit and withdrawal workflow for native CKB on testnet.

The withdrawal flow now:

- Validates the recipient CKB address
- Validates the transfer amount and occupied capacity
- Builds the transaction with CCC
- Requests wallet approval and signing
- Broadcasts the transaction to CKB testnet
- Tracks pending, completed, and failed states
- Links transactions to the CKB Explorer

The deposit flow now refreshes the connected wallet balance and records detected balance increases in the activity history.

### Implemented Phase 5 - Testing and Demo Preparation

Added automated testing with Vitest and Playwright.

**Unit tests cover:**

- Goal creation
- Invalid target amounts
- Goal assignment and removal
- Assignment above available wallet balance
- Goal completion
- Activity creation and status updates
- Balance formatting

**Browser tests cover:**

- Creating a savings goal
- Assigning funds
- Viewing goal progress
- Removing assigned funds
- Reviewing activity history
- Displaying wallet balance and deposit information
- Desktop and mobile layouts

### Improved the Savings MVP

Completed the main personal savings experience:

- Wallet connection
- Live CKB testnet balances
- Savings goal creation
- Goal progress tracking
- Deposit address and QR code
- Native CKB withdrawals
- Transaction activity
- Wallet preferences

Added validation to prevent users from assigning more funds to goals than the available wallet balance.

### Prepared Demo Documentation

Created documentation for presenting and evaluating the MVP:

- Demo setup and presentation flow
- Manual CKB testnet checklist
- Demo failure and fallback plan
- Public testnet deployment checklist
- Production-readiness assessment
- Phase 6 and Phase 7 roadmap

The current build is ready for a controlled CKB testnet demo after completing a funded-wallet rehearsal. It is not yet ready for mainnet or real customer funds.

### Verified the Build

Ran:

```bash
pnpm lint
pnpm test
pnpm test:e2e
pnpm build
```

Verification results:

- 10 unit tests passed
- 4 desktop/mobile browser flows passed
- ESLint passed
- Production build completed successfully

The application generated the expected routes:

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
| ckb-fiber-save | Non-custodial CKB savings MVP with savings goals, deposits, signed withdrawals, and activity tracking | [GitHub](https://github.com/Hallab7/ckb-fiber-save) |

## Key Concepts Learned

- Building and signing native CKB transfers with CCC
- Validating occupied capacity before creating transaction outputs
- Tracking CKB transactions from broadcast to confirmation
- Detecting deposits through wallet balance changes
- Testing local-storage application logic with Vitest
- Testing wallet-dependent user flows with Playwright
- Running browser tests against a production Next.js build
- Separating controlled demo readiness from production readiness
- Why testnet functionality still requires security, persistence, and operational work before mainnet


Important remaining work:

- Complete the funded-wallet manual testnet checklist
- Deploy the frontend to a stable public testnet URL
- Verify supported wallet connectors on the deployed domain
- Add production metadata persistence
- Replace balance-delta deposit detection with transaction indexing
- Begin Phase 6 Fiber payment and remittance integration
