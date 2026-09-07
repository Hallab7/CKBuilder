# Week 17 - September 1-7 2026

Seventeenth week of the CKBuilder program. Started building **CKB Passport**, a proof of concept for registering passkeys as `did:ckb` verification methods and using those keys for Sign-In with CKB DID without giving the application wallet spending authority.

This week focused on proving the security properties behind the flow: conflict-safe DID resolution, proof of possession before registration, WebAuthn origin and challenge binding, replay protection, DID-only sessions, and explicit negative test cases. The project was also deployed as a guided testnet demo.

## What I Did

### Defined the Passport Proof Boundary

Converted the Passport concept into four testable claims:

- A DID resolves only when exactly one matching live cell exists
- A signature is verified against the key stored in the DID document
- A passkey can be registered as the DID's `auth-1` verification method
- A user can sign in with that passkey without connecting a wallet or granting spend authority

The proof plan separates a working happy path from the negative cases that must fail before each claim can be considered closed.

### Built Canonical SIWD and Key Primitives

Implemented the shared Sign-In with DID message and key handling required by both the browser and verifier.

The implementation includes:

- Canonical SIWD message construction and parsing
- Domain, URI, version, network, timestamp, and DID validation
- Base64url, hexadecimal, and byte conversion helpers
- P-256 and secp256k1 `did:key` parsing
- Passkey COSE public-key conversion to `did:key:zDna...`
- Raw ECDSA signature parsing
- Low-S signature enforcement to reject malleable signatures

### Implemented Conflict-Safe DID Resolution

Built a security-path resolver that queries live cells by the exact DID type script and checks the result count directly.

Resolution now:

- Rejects a DID when no live cell exists, including a deactivated DID
- Rejects duplicate live cells instead of silently selecting one
- Decodes the DID document from the single accepted cell
- Selects the requested verification method by key ID
- Rejects missing keys and unsupported key encodings

A synthetic duplicate-cell fixture verifies the fail-closed behavior that cannot normally be produced on testnet.

### Added Passkey Registration and DID Updates

Implemented the wallet-authorized registration flow for adding a passkey to a DID document.

The registration path now supports:

- CCC wallet connection
- Listing all DIDs owned by the connected lock
- Explicit DID selection when a wallet controls more than one DID
- Creation of one discoverable platform passkey
- Proof of possession before an update transaction can be built
- A user-triggered wrong-key test that demonstrates registration refusal
- Wallet approval of the DID update
- Fee completion for the transaction
- Transaction confirmation waiting
- Transaction hash and explorer evidence
- Byte-for-byte comparison between the registered key and re-resolved `auth-1`

Wallet access is limited to discovering and updating the DID. It is not accepted as the application's sign-in proof.

### Built SIWD Verification and DID-Only Sessions

Implemented software-key and WebAuthn verification modes against the verification method resolved from the DID document.

The verifier checks:

- Expected application domain and URI origin
- Network, version, issuance, and expiration constraints
- One-time nonce validity
- WebAuthn response type
- Browser origin
- Challenge binding to the canonical SIWD message
- RP ID hash
- User-presence flag
- Curve and signature compatibility
- Signature validity against the DID document key

Nonces are reserved during verification and committed only after the DID, key, and signature checks succeed. Failed proofs release the nonce, while replaying a successfully used proof is rejected.

Successful sign-in creates a server-side session containing the DID, key ID, and session timestamps. It contains no wallet address field and no transaction authority.

### Completed the Security Vector Matrix

Added a deterministic vector runner covering positive software and WebAuthn proofs plus message, resolution, signature, and WebAuthn failures.

The 23-case matrix includes:

- Wrong domain and URI origin
- Unsupported version and wrong network
- Expired and future-issued messages
- Nonce replay
- Malformed and unresolved DIDs
- Duplicate live cells
- Missing verification methods
- Unsupported multicodecs
- Wrong-key, high-S, and DER signatures
- Curve confusion
- WebAuthn origin, challenge, RP ID, user-presence, and response-type failures

Each rejected vector identifies the exact verification step that failed, making the result useful as proof rather than only a pass or fail signal.

### Built and Deployed the Guided Demo

Built a Next.js application that separates the workflow into Resolve, Register, Sign In, and Explorer tasks.

The deployed demo supports:

- CCC wallet DID discovery and multi-DID selection
- Passkey creation and proof-of-possession testing
- Wallet-authorized DID updates
- Wallet-independent passkey sign-in
- Replay and cross-origin rejection demonstrations
- Session inspection without exposing raw technical JSON
- Explorer evidence showing the transaction and re-resolved key comparison

The Explorer evidence endpoint is deployed and returns structured evidence instead of a missing-route response.

### Refined the CCC Integration Direction

Ecosystem feedback clarified that Passport should extend CCC and `did:ckb` rather than compete with CCC-based wallet authentication. JoyID already provides a user-friendly passkey wallet experience.

Passport's distinct role is therefore the DID authentication layer: registering authentication keys in the DID document, verifying application login against those keys, separating authentication authority from spending authority, and issuing DID-only sessions.

### Verified the Implementation

Verification covered:

- TypeScript builds for all Passport packages
- Next.js production build
- Core, browser, verifier, and demo test suites
- All 23 deterministic security vectors
- Duplicate-cell fail-closed behavior
- Nonce replay and invalid-proof handling
- Static audit confirming session records have no address field
- Demo, acceptance, evidence, and recording readiness checks
- Public Explorer evidence API response on the Vercel deployment

The automated and local proof suites pass. A confirmed real DID update transaction with captured explorer evidence is still required to fully close the passkey-registration claim. A recording from a fresh browser profile that has never connected a wallet is also still required to fully close the wallet-independent sign-in claim.

## Projects

| Project | Description | Link |
|---------|-------------|------|
| ckb-passport | DID-native authentication PoC with CCC-authorized passkey registration, conflict-safe DID resolution, SIWD verification, DID-only sessions, and security proof vectors | [GitHub](https://github.com/Hallab7/ckb-passport) / [Live Demo](https://ckb-passport.vercel.app/) |

## Key Concepts Learned

- Why secure DID resolution must require exactly one matching live cell
- How a duplicate-cell fixture tests fail-closed behavior that the current type script should prevent
- Why proof of possession must succeed before building a DID update transaction
- How WebAuthn binds authentication to an origin, RP ID, and message-derived challenge
- Why low-S enforcement prevents ECDSA signature malleability
- Why nonces should be consumed only after full proof verification succeeds
- How a DID-only session separates application authentication from wallet spending authority
- Why a fresh wallet-free browser profile is necessary evidence for the no-wallet claim
- How negative vectors provide stronger protocol evidence than a happy-path demonstration alone
- Why local cryptographic evidence and confirmed public testnet evidence must be reported separately
- How Passport can complement CCC and JoyID by providing DID-document-based authentication instead of another wallet login flow
