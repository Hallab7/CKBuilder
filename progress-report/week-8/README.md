# Week 8 - June 22-29 2026

Eighth week of the CKBuilder program. Continued building **FiberSave** by completing the Fiber remittance milestone, validating a real local Fiber payment flow, and deploying the frontend for public testing.

This week focused on moving FiberSave beyond the personal savings MVP into actual Fiber payment request and remittance functionality.

## What I Did

### Completed - Fiber Remittance

Implemented the first Fiber remittance layer for FiberSave.

The new remittance flow now supports:

- Creating CKB Fiber payment requests
- Generating Fiber invoices through server-side API routes
- Showing invoice QR codes on the receive page
- Copying invoice text for sharing
- Submitting Fiber invoices from the send page
- Checking payment status after submission
- Recording remittance activity in the wallet activity history

Added the following routes:

- `/receive`
- `/send`
- `POST /api/payment-request`
- `GET /api/payment-request/[paymentHash]`
- `POST /api/send-payment`

### Added a Server-Side Fiber RPC Wrapper

Built a server-side Fiber RPC module so Fiber node URLs stay out of browser code.

The RPC wrapper supports:

- `getNodeInfo`
- `createPaymentRequest`
- `getPaymentStatus`
- `getInvoiceStatus`
- `sendPayment`

It also includes deterministic mock mode when no Fiber node is configured. This keeps the app testable on Vercel or in demo environments where live Fiber nodes are not available.

Later in the week, I updated the configuration model to support separate sender and receiver nodes:

```env
FIBER_SEND_RPC_URL=http://127.0.0.1:8227
FIBER_RECEIVE_RPC_URL=http://127.0.0.1:8237
```

This matches the real local test setup, where nodeA sends payments and nodeB creates invoices.

### Ran a Live Two-Node Fiber Test

Set up and tested two local Fiber nodes:

- nodeA RPC: `http://127.0.0.1:8227`
- nodeB RPC: `http://127.0.0.1:8237`

The test process:

1. Downloaded and ran Fiber `fnn` v0.8.1.
2. Generated two local CKB testnet keys.
3. Funded both node addresses through the CKB testnet faucet.
4. Connected nodeA directly to nodeB over localhost.
5. Opened a direct channel from nodeA to nodeB.
6. Waited until the channel reached `ChannelReady`.
7. Created a live Fiber invoice from nodeB.
8. Paid the invoice from nodeA.
9. Confirmed nodeA payment status was `Success`.
10. Confirmed nodeB invoice status was `Paid`.


### Improved the Frontend and Demo Experience

Updated the FiberSave UI to expose the remittance flows:

- Added Send and Receive links to the dashboard navigation
- Added remittance filtering to the Activity page
- Added status handling for pending, paid, failed, and expired remittance states
- Updated docs and environment examples for Fiber RPC configuration
- Kept mock mode available for public demos where live nodes are not reachable


### Deployed the Frontend to Vercel

Deployed the current frontend to Vercel:

- Production URL: `https://fibersave.vercel.app`
- GitHub repository: `https://github.com/Hallab7/ckb-fiber-save`

The deployed Vercel app runs in mock Fiber mode because Vercel cannot access the local Fiber nodes running on `127.0.0.1`.


For live hosted Fiber payments, the next deployment step is to run the Next.js app and both Fiber nodes on one VPS, keeping Fiber RPC bound to `127.0.0.1` and exposing only the web app through HTTPS.

### Verified the Build

Ran:

```bash
pnpm lint
pnpm test
pnpm test:e2e
pnpm build
```

Verification results:

- 14 unit tests passed
- 10 desktop/mobile browser tests passed
- ESLint passed
- Production build completed successfully
- Live local Fiber API test completed with final status `paid`

The application generated the expected routes:

- `/`
- `/goals`
- `/goals/new`
- `/goals/[goalId]`
- `/deposit`
- `/withdraw`
- `/activity`
- `/settings`
- `/send`
- `/receive`
- `/api/payment-request`
- `/api/payment-request/[paymentHash]`
- `/api/send-payment`

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-fiber-save | Non-custodial CKB savings and remittance MVP with live local Fiber invoice creation, payment submission, and deployed frontend | [GitHub](https://github.com/Hallab7/ckb-fiber-save) |

## Key Concepts Learned

- How Fiber payment requests and invoices fit into a CKB application
- How to keep Fiber RPC calls server-side in a Next.js app
- How to run separate sender and receiver Fiber nodes for local testing
- How to fund local Fiber node wallets on CKB testnet
- How to open a direct Fiber channel and wait for `ChannelReady`
- How to distinguish mock demo mode from real Fiber settlement
- Why hosted deployments cannot use local `127.0.0.1` Fiber RPC endpoints
- How to deploy the frontend to Vercel while preserving server-side mock fallbacks
- Why a VPS deployment is needed for live hosted Fiber payments


