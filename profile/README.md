# IncoPay

Private, session-based x402 payments on Solana. Sign once, settle many — every call confidential, every fee covered.

IncoPay turns the x402 "402 Payment Required" protocol into a usable rail for consumer products: pay-per-request APIs, per-token AI inference, per-tick data feeds, in-game microtransactions. A single wallet signature opens a capped, time-bounded session; the facilitator draws down against that session on every billable request. Amounts stay encrypted end-to-end via Inco Lightning. Users never hold SOL; Kora pays the fee.

## Why this exists

Crypto lacks a usable pay-per-call primitive. Three frictions stop it:

1. **Every payment is a wallet prompt.** A user who calls a billable API ten times signs ten times. Fine for a DEX trade, unusable for a normal product surface.
2. **Amounts are public.** Merchants leak price lists; users leak spending patterns. Token-2022's confidential transfer extension is disabled on mainnet pending audits.
3. **Gas is a user-side tax.** On Solana every transaction wants SOL in the signer's wallet — a cold-start barrier for every new user.

IncoPay composes three existing primitives to fix all three at once.

## How it works

```
 ┌───────────┐    sign once     ┌──────────────┐   pay per call   ┌──────────┐
 │  Wallet   │─────────────────▶│ Facilitator  │─────────────────▶│ Merchant │
 │ (Phantom/ │  approve + msg   │ cap/expiry   │  402 settled     │   API    │
 │  Solflare)│                  │ in sqlite    │                  │          │
 └───────────┘                  └──────┬───────┘                  └──────────┘
                                       │ signAndSendTransaction
                                       ▼
                               ┌──────────────┐      ┌──────────────────┐
                               │     Kora     │─────▶│  Solana devnet   │
                               │ (fee payer)  │      │  IncoToken +     │
                               └──────────────┘      │  Inco Lightning  │
                                                     └──────────────────┘
```

1. User signs once — one on-chain `approve(cap_ciphertext)` and one Ed25519 intent message. No SOL needed; the facilitator piggy-backs a rent grant into the approve tx.
2. Merchant API returns 402 with a `PAYMENT-REQUIRED` header pointing at the session scheme.
3. Client SDK retries with `PAYMENT-SIGNATURE` carrying the sessionId. No wallet prompt.
4. Facilitator settles via IncoToken's delegated `transfer` — user balance debited, merchant credited, all amounts ciphertext. Kora signs the fee.
5. Cap is enforced twice: on-chain by Inco Lightning's `e_ge(delegated_amount, amount)`, off-chain by sqlite atomic debit + refund.

## Components

| Repo | What it is |
|---|---|
| [IncoPay](https://github.com/IncoPay/IncoPay) | Main workspace: SDK, facilitator, token-deploy CLI, Next.js demo, scaffold, test harness |
| [IncoPay-KoraFacilator](https://github.com/IncoPay/IncoPay-KoraFacilator) | Kora (Solana Foundation paymaster) configured for the IncoToken + Inco Lightning program allowlist |
| [IncoPay-docs](https://github.com/IncoPay/IncoPay-docs) | Full developer documentation: quickstart, merchant onboarding, SDK reference, facilitator API, wire format |
| `.github` | You are here |

### npm packages

- `inco-x402-sessions` — client SDK and merchant-side scheme plugin (`createSession`, `wrapFetch`, `SessionIncoScheme`)
- `create-inco-x402-sessions-facilitator` — `npm create` scaffold that drops a runnable facilitator into a fresh directory

## What it unlocks

- **Pay-per-inference AI APIs.** Per-message billing at 0.001–0.01 pUSDC with no extra prompts after session open.
- **Confidential subscriptions.** One approve for a monthly cap; the service meters silently until exhaustion.
- **Machine-to-machine payments.** Headless agents hold a session, burn it down against APIs, never see a SOL balance.
- **In-game economies.** Amounts private by default; facilitator-paid fees keep the loop smooth.
- **Streaming data feeds.** Per-tick settles under the hood while the client just calls `fetch`.

## Key design decisions

- **Custom x402 scheme `"session"`.** The canonical `exact` scheme wants a wallet prompt per call. `upto` is EVM-only (Permit2-based). We ship a new scheme on top of the v1+v2 wire protocol; any x402-aware client can negotiate it.
- **Inco Lightning over Token-2022 confidential transfer.** Token-2022's `ZkE1Gama1Proof1...` is currently disabled. Inco's TEE-based covalidator network is live and supports arbitrary confidential compute, not just transfers.
- **Dual cap enforcement.** On-chain via `delegated_amount` ciphertext (ground truth, expensive to check). Off-chain via sqlite (fast, replay-safe, atomic debit with refund-on-fail). Both running catches the failure modes of either alone.
- **Zero SOL UX.** The approve transaction includes a `SystemProgram.transfer` from facilitator to user to cover Inco Lightning's allowance-PDA rent (~0.001 SOL). User never acquires SOL; the grant is consumed inside the same tx.
- **Pluggable Kora.** Kora is the paymaster, not the facilitator. If Kora is down, the facilitator self-signs and submits via direct Solana RPC. Kora's `allowed_programs` config is whitelisted for the IncoToken + Inco Lightning program pair only.

## Trust model

- **User** trusts the facilitator to respect the session's `cap`, `recipient`, and `expirationUnix`. The cap cannot be overspent (on-chain guard); the recipient and expiry are enforced off-chain by `/verify` cross-checks.
- **Facilitator** trusts Kora and Solana RPC to submit honestly. A compromised Kora can censor or delay but cannot redirect funds — it does not sign as the transfer authority.
- **Merchant** trusts `/settle` to either return a real tx signature or refund the sqlite debit. The on-chain signature is independently auditable.

## Status

- Solana **devnet**: live. IncoToken program `9Cir3JKBcQ1mzasrQNKWMiGVZvYu3dxvfkGeQ6mohWWi` and Inco Lightning program `5sjEbPiqgZrYwR31ahR6Uk9wf5awoX61YGg7jExQSwaj` are both executable and integrated. Full end-to-end flow verified: token deployment, encrypted airdrop, `createSession`, five consecutive settles, cap enforcement, cross-restart session persistence, multi-user concurrent sessions.
- Solana **mainnet**: awaiting Inco Lightning mainnet deployment and a completed external audit of the facilitator. Production checklist published in docs.

## Get started

- [Documentation →](https://github.com/IncoPay/IncoPay-docs) — quickstart, architecture, merchant onboarding, SDK + API reference
- [Main workspace →](https://github.com/IncoPay/IncoPay) — all packages
- [Kora fork →](https://github.com/IncoPay/IncoPay-KoraFacilator) — paymaster configured for IncoPay

## License

MIT, across all repositories.
