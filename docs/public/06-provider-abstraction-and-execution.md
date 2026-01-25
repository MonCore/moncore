# Provider Abstraction & Execution Framework 
<div align="right">
← <a href="../00-doc-index.md">Back to Documentation Index</a>
</div>

---

## 1. Purpose and Scope

This document defines the execution provider abstraction and authority separation model implemented in the MonCore Financial Operating System.

The objective of this framework is to ensure that:

- MonCore remains the sole **system of record** for all balances, positions, and financial state  
- External execution providers operate strictly as **stateless execution and transport layers**  
- All business logic, compliance enforcement, ledger mutation, settlement control, and reconciliation remain exclusively inside the MonCore kernel  

This model enables:

- Issuer-led deployment and sponsorship  
- Multi-provider portability and routing  
- Scheme-safe settlement finalisation  
- Regulator-grade auditability and non-repudiation  

This document is intended for:

- Issuer sponsors and sponsor banks  
- Electronic Money Institutions (EMIs)  
- Scheme and settlement processors  
- Regulatory technical reviewers and auditors  

---

## 2. Architectural Principle — Execution vs Authority

MonCore enforces a strict separation between **execution responsibility** and **financial authority**.

### 2.1 Execution Providers

Execution providers are external institutions or processors responsible only for:

- Payment initiation and routing  
- Card transaction authorisation and capture  
- Bank transfer execution  
- Identity verification workflows  
- Scheme and clearing connectivity  

Execution providers:

- Never write balances  
- Never mutate financial state  
- Never resolve ledger authority  
- Never compute exposure, limits, or compliance decisions  
- Never act as system of record  

Execution providers are treated as **stateless, replaceable execution layers**.

---

### 2.2 Kernel Authority (MonCore)

The MonCore kernel exclusively owns:

- Ledger mutation and balance computation  
- Single-writer financial authority  
- Velocity limits and rolling exposure counters  
- AML / CTF enforcement and freezes  
- FX conversion and precision control  
- Idempotency and replay protection  
- Dispute accounting and compensating entries  
- Settlement finalisation and reconciliation  
- Cryptographic audit and non-repudiation  

All credits, debits, holds, reversals, and dispute adjustments are performed **only inside the MonCore ledger**.

---

## 3. Provider Abstraction Layer

All external providers are integrated through dedicated **stateless provider adapters**.

Each adapter enforces the following contract:

- No database access  
- No ledger access  
- No business rule evaluation  
- No persistence  
- No state ownership  

Adapters are responsible only for:

- Canonical payload construction  
- Provider authentication and cryptographic signing  
- Request execution  
- Raw response return  

Typical adapter categories include:

- Open-Banking Execution Provider  
- Card Processing Provider  
- Issuer / Settlement Processor  
- Identity Verification Provider  

This design guarantees:

- Provider replacement without business refactoring  
- Multi-provider support per execution domain  
- Issuer-controlled execution routing  
- Zero ledger coupling with providers  

---

## 4. Kernel-Controlled Execution Flow

All externally executed financial flows follow a uniform authoritative sequence controlled by the kernel.

### 4.1 Pre-Execution Controls (Kernel)

Before any provider call is made, the kernel performs:

- Tier and product limit enforcement  
- Velocity and rolling exposure evaluation  
- AML / CTF screening and freeze checks  
- FX normalisation and precision validation  
- Correlation identifier generation  
- Idempotency key registration  

No external execution request is issued unless kernel authorisation succeeds.

---

### 4.2 Provider Execution (Stateless)

The provider adapter:

- Receives a fully validated, pre-authorised payload  
- Applies provider-specific signing and authentication  
- Executes the external request  
- Returns provider references and execution metadata  

At this stage:

- No ledger mutation occurs  
- No balances are modified  
- No financial authority is transferred  

---

### 4.3 Webhook-Driven Finalisation

All financial state mutation occurs only when:

- A verified provider webhook is received  
- Raw body signature verification succeeds  
- Timestamp and replay protection pass  
- Idempotency guards succeed  

Only then does the kernel:

- Insert immutable ledger entries  
- Update wallet balances  
- Apply holds or reversals  
- Write cryptographically hashed audit records  
- Update exposure and velocity counters  

This guarantees that:

- Provider retries cannot double-credit  
- Partial executions cannot corrupt state  
- The kernel remains the sole writer of financial truth  

---

## 5. Idempotency and Replay Protection

MonCore enforces **exactly-once semantics** across all execution domains.

### 5.1 API-Level Idempotency

Each mutating request requires:

- `X-Idempotency-Key`  
- Correlation identifier  

The kernel:

- Registers the key before execution  
- Blocks concurrent duplicates  
- Rejects in-flight retries with conflict status  
- Replays completed responses deterministically  

Responses are persisted and replayed with original correlation identifiers and timestamps.

---

### 5.2 Webhook-Level Idempotency

Each provider webhook:

- Is cryptographically verified  
- Is replay-protected by timestamp and digest  
- Is deduplicated using a processed-event registry  

Duplicate webhooks are ignored without side effects.

---

## 6. Webhook Trust Boundary

All provider webhooks are processed under a hardened trust boundary.

Controls include:

- Raw body verification (no JSON re-serialization)  
- HMAC or asymmetric signature validation  
- Timestamp skew enforcement  
- Replay detection and deduplication  

No database write is permitted until:

- Signature verification succeeds  
- Payload integrity is confirmed  

This guarantees:

- Forgery resistance  
- Replay immunity  
- Provider non-authority enforcement  

---

## 7. Ledger Authority Model

MonCore enforces a strict **single-writer financial ledger**.

Guarantees:

- All credits and debits are written exclusively by the kernel  
- All balances are derived from immutable ledger entries  
- Providers never post balances or resolve state  

Controlled operations include:

- Open-banking wallet funding  
- Card top-ups and reversals  
- Internal transfers and FX conversions  
- Dispute holds and releases  
- Withdrawals and payout reversals  

Every mutation produces:

- Immutable ledger entry  
- Transaction record  
- Audit event with cryptographic hash  
- Exposure and velocity update  

---

## 8. Disputes, Holds, and Chargeback Safety

Disputes are handled entirely inside the kernel.

Mechanisms include:

- Early dispute holds when user mapping is incomplete  
- Negative compensating ledger entries for held funds  
- Deterministic recredit flows on resolution  
- Immutable tracking of charge and authorisation identifiers  

This ensures:

- Scheme-safe accounting  
- No provider-side balance dependency  
- Full forensic traceability  

---

## 9. Settlement Finalisation and Non-Repudiation

Issuer settlement finalisation is performed by a dedicated kernel service.

Features include:

- Hard idempotency guard per settlement identifier  
- Ledger debit verification before finalisation  
- Canonical settlement payload construction  
- Cryptographic system signatures for non-repudiation  

Each finalised settlement produces:

- Ledger debit (if not already present)  
- Settlement record  
- Canonical payload hash  
- System-signed non-repudiation signature  
- Immutable audit event  

This guarantees:

- Exactly-once settlement  
- Tamper-proof evidence  
- Regulator-grade forensic integrity  

---

## 10. Reconciliation Framework

Daily reconciliation compares:

- Issuer custody balance snapshots  
- Kernel ledger balances  

Process:

- Currency-level aggregation  
- Precision-safe tolerance evaluation  
- Per-user mismatch expansion  
- Daily reconciliation report generation  
- Cryptographic signing of reconciliation results  

On mismatch:

- Impacted users are isolated  
- Differences are recorded per user and currency  
- Audit trails are generated automatically  

This framework supports:

- EMI safeguarding obligations  
- Sponsor bank daily proof of funds  
- Regulatory inspection readiness  

---

## 11. Provider Portability Guarantee

Because providers are fully abstracted:

- No business logic references provider-specific semantics  
- No ledger coupling exists  
- No compliance coupling exists  
- No settlement coupling exists  

Any execution provider can be:

- Added  
- Replaced  
- Parallelised  

Without modifying:

- Ledger engine  
- AML logic  
- Limits and velocity rules  
- Audit subsystem  
- Settlement and reconciliation engines  

---

## 12. Threat Model Summary

The architecture explicitly protects against:

- Double credits from retries  
- Replay of provider webhooks  
- Forged settlement messages  
- Partial execution failures  
- Duplicate settlement capture  
- Provider state divergence  
- Ledger corruption through external mutation  

All financial authority remains inside MonCore.

---

## 13. Issuer and Sponsor Bank Positioning

MonCore operates as:

- System of record  
- Ledger authority  
- Compliance enforcement engine  
- Settlement controller  
- Audit and reconciliation authority  

Execution providers operate only as:

- Transport  
- Routing  
- Scheme connectivity  

This model is aligned with:

- EMI safeguarding frameworks  
- Issuer-led card programmes  
- Scheme separation of duties  
- Regulator-grade operational governance  

---

## 14. Conclusion

MonCore enforces a provider-agnostic, issuer-controlled execution architecture in which:

- All financial authority remains inside the kernel  
- All execution providers are stateless and replaceable  
- All settlement and reconciliation are cryptographically auditable  

This framework enables MonCore to operate safely as a financial operating system for:

- Issuer-led banking  
- Multi-provider execution environments  
- Regulated embedded finance platforms  

