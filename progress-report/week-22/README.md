# Week 22 - October 6-12 2026

Twenty-second week of the CKBuilder program. Continued **CKB Automata** by tightening the DAO harvest submission experience, improving automation guidance and list performance, correcting owner compensation behavior, and bounding executor queue history.

This week focused on the gaps between a valid protocol and a usable service. A transaction broadcast by the wallet should appear immediately, large automation lists should not wait on expensive aggregates, and contract rules should permit the payout choices that the reviewed setup flow promises.

## What I Did

### Made New Harvest Submissions Visible Immediately

Added a pending-submission registration path for DAO harvest transactions.

After a wallet broadcasts a setup transaction, the application can now:

- Register the transaction hash without signing or broadcasting on the server
- Return the pending record before the canonical indexer reaches it
- Merge pending and indexed records without duplicate display
- Preserve the successful broadcast when registration is temporarily unavailable
- Remove the provisional entry after canonical indexing takes over

This closes the confusing interval where the wallet has sent a transaction but the dashboard still looks empty.

### Added Workflow-Specific Automation Guides

Reworked the product guide into focused tabs for the implemented automation workflows.

The guide now separates:

- One-time deadline automation
- Recurring native CKB payments
- Nervos DAO harvest automation
- Owner recovery and cancellation expectations

The content keeps wallet signing, executor actions, contract enforcement, chain confirmation, and owner controls distinct instead of describing automation as one opaque background action.

### Improved Automation List Performance

Changed list reads so the first page of jobs can render before server-wide aggregate totals are available.

The compact path now:

- Returns page records without deriving every recipient total
- Keeps the complete filtered count separate from visible page values
- Preserves stable pagination and canonical ordering
- Shows a page-shaped loading state
- Fetches aggregate data only where the interface needs it

This reduces unnecessary chain-derived work on the critical dashboard path while preserving exact totals in the detailed views.

### Corrected Owner Compensation Payouts

Updated the DAO harvest contract and builders so the owner lock can also be the committed compensation destination.

The corrected rule still requires:

- The exact payout lock committed during setup
- Exact compensation derived from the DAO withdrawal
- Exact principal redeposit for a continuing cycle
- Separate executor reward accounting
- Contract-controlled output positions
- Unchanged owner, vault, DAO type, and cycle terms

The fix removes an unnecessary product restriction without giving the executor discretion over the recipient or amount.

### Published the Corrected Compensation Policy

Added a migration and updated the testnet deployment manifests for the owner-compensation policy.

Deployment metadata records the replacement code hash and cell dep so the API and frontend can reject stale or mismatched identities. The update is a contract publication record, not proof that a complete real DAO cycle finished successfully.

### Bounded Executor Queue History

Added explicit retention limits to the executor queues.

The queue policy now keeps retry and history data finite while retaining enough terminal evidence for diagnosis. This prevents completed and failed work records from growing without a bound in a long-running operator instance.

### Accepted Shared Harvest Payout Addresses

Adjusted API ownership checks so multiple harvest automations may intentionally use the same committed payout address.

The API still verifies each job and owner scope independently. Sharing a payout destination does not merge job identity, principal, cycle state, executor authorization, or recovery authority.

### Preserved the Wallet Review Boundary

Kept the wallet review as the final authority over the setup transaction after the API and UI changes.

The submission flow continues to:

- Compare displayed harvest terms with serialized outputs
- Recheck live inputs before invoking the wallet
- Block changed quotes or stale outpoints
- Record wallet rejection without a false submission
- Preserve a successful broadcast across registration outages
- Avoid server-side owner signing material

### Added Targeted Regression Coverage

The eight source commits dated in this reporting window added or expanded tests for:

- Pending submission registration and canonical handoff
- Generated API-client request types
- Workflow guide content
- Compact list and total-count behavior
- Owner compensation to the owner lock
- Deployment migration ordering
- Queue retention bounds
- Shared payout-address handling

The source history contains no additional Automata commits after October 7 within this week.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-automata | Testnet-only CKB automation platform hardened with immediate harvest-submission visibility, workflow guides, compact list reads, corrected owner compensation, bounded executor queues, and shared payout-address support | [GitHub](https://github.com/Hallab7/ckb-automata) |

## Key Concepts Learned

- Why a successful wallet broadcast and a canonically indexed record are different states
- How provisional submissions improve feedback without replacing chain confirmation
- Why expensive aggregate reads should not block the first useful page of a dashboard
- How a product restriction can be removed while preserving an exact contract commitment
- Why code-hash and cell-dep updates must move together across manifests, API, and frontend
- How bounded queue retention controls operational storage without erasing immediate diagnostics
- Why a shared payout address does not imply shared ownership or shared job identity
- How generated API types keep pending-submission behavior aligned across service and browser
- Why wallet review must survive frontend and API changes as an independent safety boundary
- Why a published replacement contract still needs live workflow and monitoring evidence
