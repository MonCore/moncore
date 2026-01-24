# System Architecture — MonCore Financial Operating System

---

## 1. Overview

MonCore is a production-grade financial operating system designed to act as the core execution and control layer for regulated digital money, card, and payment platforms.

MonCore is not a consumer application and not a bank.

It is a multi-tenant financial kernel that provides:

- Canonical ledger and balance authority  
- Embedded compliance and governance  
- Deterministic transaction execution  
- Reconciliation and settlement control  
- Provider-agnostic execution interfaces  

The platform is designed for issuer-led deployment under EMI / bank supervision, with strict separation between internal financial state and external execution providers.

---

## 2. High-Level Architecture

MonCore is structured as three strictly separated planes:


┌──────────────────────────────────────────────┐
│                Product Plane                 │
│  (Wallets, Cards, QR, Transfers, Dashboards) │
└──────────────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────┐
│           MonCore Kernel (Control Plane)     │
│                                              │
│  • Ledger Engine & Snapshots                 │
│  • Idempotency & Correlation                 │
│  • Compliance & AML                          │
│  • Exposure & Counters                       │
│  • Reconciliation & Settlement               │
│  • Audit & Evidence                          │
│  • Tenant & Capability Gating                │
└──────────────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────┐
│             Execution Plane                  │
│  (Issuers, Safeguarding, Cards, Open Banking │
│   FX, Identity, Payments)                    │
└──────────────────────────────────────────────┘

Only the kernel is authoritative.  
External providers are execution agents only.

---

## 3. Core Ledger & Balance Architecture

### 3.1 Canonical Ledger

All monetary state is derived exclusively from the canonical ledger.

Every financial operation generates one or more immutable ledger entries containing:

- Transaction identifier  
- User and tenant ownership  
- Currency and signed amount  
- Execution reference  
- FX attribution (when applicable)  
- Status lifecycle (CREATED, AUTHORIZED, SETTLED, REVERSED)  

Ledger rows are immutable and cannot be deleted.

Uniqueness constraints enforce:

- One posting per transaction / user / currency  
- One settlement per provider reference  
- No duplicate charge identifiers  

This guarantees:

- No double posting  
- No duplicate settlement  
- Deterministic replay safety  

### 3.2 Balance Snapshots

Authoritative balances are always derived from the ledger.

Balance snapshots provide controlled materialized views per:

- User  
- Currency  

Snapshots are regenerated from ledger aggregation and protected by uniqueness constraints.

Snapshots are a performance layer only.  
The ledger remains the single source of truth.

---

## 4. Multi-Tenant Isolation Model

MonCore supports multiple regulated tenants operating on the same kernel.

Isolation is enforced by:

- Tenant ownership on all user records  
- Tenant-scoped wallets and counters  
- Tenant-scoped credentials and API keys  
- Dedicated partner and admin audit planes  

Tenants cannot observe or influence any other tenant’s data.

All aggregation views operate strictly through tenant-scoped joins.

---

## 5. Deterministic Execution & Idempotency

All financial requests are protected by kernel-level idempotency.

The idempotency layer enforces:

- One execution per logical request  
- Verified response replay  
- Correlation identifier propagation  

This guarantees:

- Exactly-once posting  
- Safe retries  
- No double debit or credit under webhook duplication or provider replay  

Correlation identifiers propagate across:

- Ledger entries  
- Audit logs  
- Reconciliation flows  
- Compliance timelines  

---

## 6. Embedded Compliance & Risk Control

Compliance is embedded directly into the kernel.

Native subsystems include:

- AML event logging  
- Velocity and rolling counters  
- Case management and decisions  
- Sanctions and PEP screening  
- Geographic restrictions and account freezing  

Before any ledger posting is executed:

- Velocity counters are evaluated  
- Tier limits are enforced  
- AML risk is assessed  

Blocked transactions never reach the ledger.

---

## 7. Exposure & Regulatory Counters

MonCore computes real-time regulatory exposure directly from the ledger.

The counters subsystem records:

- Per-user rolling volumes  
- Per-product regulatory counters  
- Timestamped velocity windows  

Tenant-level exposure provides:

- Total ledger balance  
- Monthly transaction volume  
- Monthly FX volume  
- Total cards issued  

All exposure metrics are ledger-only and provider-independent.

---

## 8. Provider-Agnostic Execution Layer

External providers are integrated behind stable kernel interfaces.

Execution domains include:

- Open banking funding  
- Card funding  
- Card authorisation and clearing  
- Card settlement  
- Bank withdrawals  

Each provider interaction is:

- Idempotent  
- Correlated  
- Audited  
- Reconciled back into the ledger  

Providers can be replaced without changing:

- Ledger model  
- Compliance model  
- Reconciliation logic  
- Audit exports  

---

## 9. Reconciliation & Settlement Control

MonCore implements a native settlement and reconciliation engine.

Settlement records include:

- Provider reference  
- Amount and currency  
- Payload hash  
- System signature  
- Processing timestamps  

Daily reconciliation produces:

- Issuer vs backend balance reports  
- Exception mismatch records  
- Settlement adjustments  

This ensures:

- No double settlement  
- No missing settlement  
- Cryptographically provable settlement lineage  

---

## 10. Immutable Audit & Evidence Layer

All critical operations generate immutable audit records.

Audit events include:

- Financial intents and results  
- Provider callbacks  
- Compliance decisions  
- Admin and partner actions  
- Identity lifecycle events  

Integrity is enforced by:

- Payload hashing  
- Provider + payload uniqueness  
- Export immutability controls  

Derived regulator-safe views include:

- Admin actions timeline  
- Compliance timeline  
- Transaction lifecycle  
- Compliance transaction views  

All exports are tracked with:

- File hashes  
- Row counts  
- Time scopes  
- Operator identity  

---

## 11. Tenant, Partner & Admin Control Planes

MonCore separates three governance planes.

### 11.1 Product Plane

End-user operations:

- Wallets and balances  
- Transfers and QR payments  
- Cards and funding  
- Authentication and sessions  

### 11.2 Partner / Tenant Plane

Tenant operators access:

- Tenant dashboards  
- Compliance timelines  
- Ledger and exposure exports  
- Partner audit trails  

All partner actions are fully audited.

### 11.3 Internal Admin Plane

Internal governance is isolated through:

- Dedicated admin identities  
- Segmented sessions  
- Immutable admin audit logs  

Admin access is regulator-safe and read-only for financial state.

---

## 12. Sandbox = Production Parity

MonCore enforces full environment parity.

The same:

- Ledger model  
- Compliance rules  
- Counters  
- Reconciliation jobs  
- Audit paths  

Run in both sandbox and production.

Only the following differ:

- Credentials  
- Counterparties  
- Settlement endpoints  

This enables:

- Pre-issuer sandbox audits  
- Regulator walkthroughs  
- Pilot deployments without migrations  

---

## 13. Supported Deployment Models

### 13.1 Kernel-Only Integration

MonCore provides:

- Ledger  
- Compliance  
- Reconciliation  
- Exposure  

Partner supplies all frontend and orchestration.

### 13.2 Full Stack Platform

MonCore provides:

- Wallet APIs  
- Cards and funding  
- QR and transfers  
- Realtime telemetry  
- Admin and partner dashboards  

### 13.3 Pilot / Pre-Issuer Mode

Before issuer onboarding, MonCore can operate with:

- Open banking funding  
- Card funding providers  
- Full AML and ledger enforcement  
- Full audit and reconciliation  

Issuer-dependent settlement remains contract-gated.

---

## 14. Security & Integrity Guarantees

MonCore provides:

- Immutable ledger  
- Deterministic idempotency  
- Snapshot derivation  
- Payload hashing  
- System signatures  
- Full audit lineage  

The kernel guarantees:

- No balance desynchronisation  
- No double debit or credit  
- No settlement duplication  
- Full forensic traceability  

---

## 15. Conclusion

MonCore is a financial operating system designed to provide issuer-grade correctness, compliance, and governance for modern regulated platforms.

By separating ledger authority, compliance enforcement, and provider execution, MonCore enables:

- Rapid deployment of regulated products  
- Provider independence  
- Regulator-grade auditability  
- Zero-migration issuer onboarding  

MonCore acts as a stable financial kernel capable of powering multiple regulated products under a single governed control plane.

