# IncoPay

**Private payments on Solana — X402-style confidential transactions with gasless UX.**

IncoPay is a next-generation payment primitive that combines **confidential transfers**, **message-based authorization**, and **facilitator-paid gas** to enable seamless, private micropayments on Solana.

We extend the x402 model into a privacy-first system:
- **Sign once → authorize payment**
- **Sign once → confirm transaction**
- **Zero gas paid by the user**
- **Amounts remain confidential**

---

## 🌐 What we are building

IncoPay brings **private, programmable payments** to Solana by combining:

- Confidential token transfers via Inco Network
- Message-based payment authorization (X402-style)
- Gas abstraction using a facilitator (Kora)

This allows users to make payments that are:
- **Private** (amounts hidden)
- **Gasless** (no SOL required)
- **Frictionless** (minimal signatures)

---

## ⚡ Why IncoPay

Current crypto payments have major limitations:

- ❌ Amounts are publicly visible  
- ❌ Users must pay gas for every transaction  
- ❌ Poor UX for repeated or micro interactions  

IncoPay solves this by introducing:
- **Confidential payments by default**
- **Facilitator-paid gas**
- **Streamlined authorization flow**

---

## 🔑 Key Advantages

### 1. Private by Default
- Payment amounts are encrypted (ciphertext)
- Data remains confidential throughout the flow
- Only revealed when necessary

### 2. Gasless Experience
- Users never pay transaction fees
- Facilitator (via Kora) handles fee payment
- Ideal for onboarding non-crypto users

### 3. Minimal Signatures
- One signature for authorization
- One signature for transaction
- No repeated wallet prompts per interaction

### 4. X402-style Payments
- Message-based authorization model
- Compatible with pay-per-use APIs and services
- Designed for programmable payments

### 5. Modular Architecture
- Works with existing Solana ecosystem
- Pluggable facilitator layer
- Extensible for different use cases

---

## 🧠 What this enables

IncoPay unlocks new categories of applications:

### 🤖 AI & Agents
- Private pay-per-inference APIs
- Autonomous agents making confidential payments
- Usage-based AI billing without exposing data

### 🎮 Games
- Hidden in-game economies
- Private microtransactions
- Seamless player experience without gas friction

### 🌐 APIs & SaaS
- Pay-per-request APIs with confidential pricing
- Monetization without exposing usage patterns
- Better UX for developers and users

### 💬 Real-time Apps
- Chat systems with private per-message payments
- Streaming payments with hidden amounts
- Interactive apps with continuous settlement

---

## 🏗️ Core Architecture

IncoPay is built around three main components:

### 🧩 User
- Signs a payment authorization message
- Signs the transaction once
- Never pays gas

### ⚙️ Facilitator
- Verifies user authorization
- Constructs transaction payloads
- Forwards signed transactions

### ⛽ Fee Payer (Kora)
- Adds fee-payer signature
- Submits transaction to Solana
- Ensures gasless UX

### 🔒 Inco Network
- Handles confidential token transfers
- Keeps amounts encrypted via ciphertext
- Powers privacy layer

---

## 🔐 Trust Model

IncoPay separates concerns clearly:

- **On-chain**
  - Signature verification (Ed25519)
  - Transaction execution
  - Confidential transfer logic

- **Off-chain (Facilitator + Kora)**
  - Transaction orchestration
  - Fee payment
  - Request lifecycle management

This ensures:
- Privacy of transaction data
- Smooth user experience
- Reliable execution flow

---

## 🚀 Vision

IncoPay is building toward a future where:

- Payments are **invisible but verifiable**
- Privacy is **default, not optional**
- Users interact without worrying about gas or friction

We believe the next evolution of payments is:
> **Private, programmable, and user-invisible**

---

## 📦 Ecosystem

The IncoPay ecosystem includes:

- **Frontend apps** — real-world payment flows
- **Facilitator service** — payment lifecycle management
- **On-chain programs** — confidential transfer logic
- **Integrations** — Inco Network and Kora

---

## 🧩 Built for builders

IncoPay is designed for:

- Developers building on Solana
- Teams exploring confidential finance
- Founders creating AI, gaming, and API products
- Anyone who wants **better UX + privacy in payments**

---

## 📜 License

MIT
