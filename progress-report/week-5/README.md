# Week 5 — May 30–June 6 2026

Fifth week of the CKBuilder program. Explored the Fiber Network — CKB's Lightning-compatible payment channel protocol — by building a pay-per-access content platform.

## What I Did

### Designed the Project

Picked **Fiber Content Gate** as the week's project — a pay-per-access content platform where creators publish gated articles/posts and readers unlock them with Fiber micropayments. No subscriptions, no platform cut, instant settlement.

Key design decisions:
- No custom CKB scripts — this project is purely Fiber Network + Next.js
- Operator (server) holds a Fiber node and generates invoices
- Content is encrypted server-side; decryption key only released after payment confirmed
- Mock invoice fallback for development without a live Fiber node
- AES-256-GCM for content encryption

### Learned the Fiber Network

Studied how Fiber payment channels work:

- **Payment channel** — two nodes lock CKB on-chain to create a channel, then transact off-chain instantly. One funding tx, unlimited payments, one settlement tx.
- **Invoice** — a BOLT11-style payment request string containing: amount, payment hash, expiry, node pubkey. Readers pay the invoice; Fiber routes it through the network.
- **HTLC** — Hash Time-Locked Contracts enable trustless multi-hop routing
- **Currency** — Fiber supports CKB and xUDT tokens in the same channel. Testnet uses `Fibb` currency.

Key Fiber RPC methods:
- `new_invoice` — generates a payment request (receiver side)
- `get_invoice` — polls invoice status: `Open → Received → Paid`
- `open_channel` — establish a channel with another node
- `get_node_info` — get node pubkey and addresses
- `send_payment` — pay an invoice (sender side)

### Built the Fiber Node Setup

Created the full node setup infrastructure in `fiber-node/`:

- `docker-compose.yml` — runs the official `ghcr.io/nervosnetwork/fiber:latest` image
- `config.toml` — testnet configuration (CKB RPC, P2P listening address, RPC port 8228)
- `verify-node.ps1` — PowerShell script that calls all 4 key RPC methods and confirms the node is working
- `README.md` — step-by-step guide: start Docker, fund node, open channel, test invoice

### Built the Frontend (Next.js)

Complete Next.js app at `fiber-content-gate/frontend/` — builds cleanly, all 10 routes generated.

**Pages:**
- `/` — Home with product summary, live stats (published count, paid unlocks, CKB processed)
- `/browse` — Browse all content with price filter
- `/content/[contentId]` — Content detail with payment unlock panel (QR code + polling)
- `/publish` — Creator form: title, preview, full content, price in CKB
- `/dashboard` — Creator metrics and content management table

**API Routes:**
- `POST /api/content` — create content (encrypts full text server-side)
- `GET /api/content/[contentId]` — public metadata + preview
- `POST /api/invoice` — generate Fiber invoice (falls back to mock if node unavailable)
- `GET /api/invoice/[paymentHash]` — poll payment status; returns decrypted content on paid

**Core libraries:**
- `lib/fiber-rpc.ts` — Fiber JSON-RPC client (`newInvoice`, `getInvoice`, `getNodeInfo`)
- `lib/content-store.ts` — AES-256-GCM encryption, JSON file storage, CKB/shannon conversion
- `lib/payment-store.ts` — payment record tracking with status, expiry, amount
- `lib/validators.ts` — input validation

**Payment UI flow:**
1. Reader clicks "Pay to Unlock"
2. Frontend calls `/api/invoice` → Fiber node generates invoice
3. QR code rendered from invoice string
4. Frontend polls `/api/invoice/[paymentHash]` every 2 seconds
5. On `Paid` status → full content decrypted and rendered


## Projects

| Project | Description | Link |
|---------|-------------|------|
| fiber-content-gate | Pay-per-access content platform using Fiber micropayments | [GitHub](https://github.com/Hallab7/fiber-content-gate) |

## Key Concepts Learned

- How Fiber payment channels work — open/fund/transact/close lifecycle
- BOLT11 invoice format and HTLC-based routing
- The difference between on-chain CKB transactions and off-chain Fiber payments
- Why Fiber enables sub-cent micropayments that are impractical on-chain
- Fiber RPC API — `new_invoice`, `get_invoice`, status lifecycle (`Open → Received → Paid`)
- Server-side content encryption/decryption — only released after payment proof
- Mock invoice fallback pattern for developing without a live node
- How Fiber's multi-token support works (CKB + xUDT in same channel)
- Payment channel liquidity — both sides need inbound/outbound capacity
