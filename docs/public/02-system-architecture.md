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

The platform is designed for issuer-led deployment under EMI, PSD2, scheme, and bank supervision, with strict separation between internal financial state and external execution providers.

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

External providers act strictly as execution agents and never hold authority over ledger state, balances, compliance state, or reconciliation outcomes.

---

## 3. Data Custody & Legal Boundary

MonCore never holds legal custody of client funds and never acts as:

- Issuer  
- Safeguarding institution  
- Settlement agent  
- Payment institution  

All client funds are held exclusively by licensed third-party institutions.

MonCore maintains internal representations of:

- Balances  
- Compliance state  
- Exposure state  
- Audit and reconciliation evidence  

Legal custody, safeguarding, and settlement remain the sole responsibility of licensed execution institutions.

---

## 4. Core Ledger & Balance Architecture

### 4.1 Canonical Ledger

All monetary state is derived exclusively from the canonical ledger.

Every financial operation generates one or more immutable ledger entries containing:

- Transaction identifier  
- User and tenant ownership  
- Currency and signed amount  
- Execution provider reference  
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

---

### 4.2 Balance Snapshots

Authoritative balances are always derived from the ledger.

Balance snapshots provide controlled materialized views per:

- User  
- Currency  

Snapshots are regenerated exclusively from ledger aggregation and protected by uniqueness and integrity constraints.

Snapshots are a performance layer only.

The ledger remains the single source of truth.

---

## 5. Disaster Recovery & State Reconstruction

Because all monetary state is derived from immutable ledger postings, MonCore supports full forensic reconstruction of:

- Balances  
- Exposures  
- Settlement state  
- Transaction lifecycles  

No in-memory or ephemeral financial state is authoritative.

This enables:

- Full balance recovery  
- Settlement re-derivation  
- Audit replay  
- Regulator forensic review  

After system failure, corruption, or operator error, the complete financial state can be reconstructed deterministically from ledger history alone.

---

## 6. Multi-Tenant Isolation Model

MonCore supports multiple regulated tenants operating on the same kernel.

Isolation is enforced by:

- Tenant ownership on all user records  
- Tenant-scoped wallets and counters  
- Tenant-scoped credentials and API keys  
- Dedicated partner and admin audit planes  

Tenants cannot observe or influence any other tenant’s data.

All aggregation views operate strictly through tenant-scoped joins.

All regulated execution domains remain disabled until explicitly activated by contract and jurisdiction.

---

## 7. Deterministic Execution & Idempotency

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

## 8. Embedded Compliance & Risk Control

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

Blocked or flagged transactions never reach the ledger.

---

## 9. Exposure & Regulatory Counters

MonCore computes real-time regulatory exposure directly from the ledger.

The counters subsystem records:

- Per-user rolling volumes  
- Per-product regulatory counters  
- Timestamped velocity windows  

Tenant-level exposure provides:

- Total ledger balances  
- Monthly transaction volume  
- Monthly FX volume  
- Total cards issued  

All exposure metrics are ledger-derived and provider-independent.

---

## 10. Provider-Agnostic Execution Layer

External providers are integrated behind stable kernel interfaces.

Execution domains include:

- Open-banking funding  
- Card funding  
- Card authorisation and clearing  
- Card settlement  
- Bank withdrawals  

Each provider interaction is:

- Idempotent  
- Correlated  
- Audited  
- Reconciled back into the ledger  

Providers may be integrated or changed without modifying:

- Ledger model  
- Compliance model  
- Reconciliation logic  
- Audit exports  

---

## 11. Reconciliation & Settlement Control

MonCore implements a native settlement and reconciliation engine.

Settlement records include:

- Provider reference  
- Amount and currency  
- Canonical payload hash  
- System signature  
- Processing timestamps  

Daily reconciliation produces:

- Issuer vs backend balance reports  
- Exception mismatch records  
- Settlement adjustment records  

This ensures:

- No double settlement  
- No missing settlement  
- Cryptographically verifiable settlement lineage  

---

## 12. Immutable Audit & Evidence Layer

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
- Transaction lifecycle views  
- Compliance transaction reports  

All exports are tracked with:

- File hashes  
- Row counts  
- Time scopes  
- Operator identity  

---

## 13. Tenant, Partner & Admin Control Planes

MonCore separates three governance planes.

### 13.1 Product Plane

End-user operations:

- Wallets and balances  
- Transfers and QR payments  
- Cards and funding  
- Authentication and sessions  

### 13.2 Partner / Tenant Plane

Tenant operators access:

- Tenant dashboards  
- Compliance timelines  
- Ledger and exposure exports  
- Partner audit trails  

All partner actions are fully audited.

### 13.3 Internal Admin Plane

Internal governance is isolated through:

- Dedicated admin identities  
- Segmented sessions  
- Immutable admin audit logs  

Admin access is regulator-safe and read-only for financial state.

---

## 14. Sandbox = Production Parity

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

## 15. Supported Deployment Models

### 15.1 Kernel-Only Integration

MonCore provides:

- Ledger  
- Compliance  
- Reconciliation  
- Exposure  

The partner supplies all frontend and orchestration.

### 15.2 Full Stack Platform

MonCore provides:

- Wallet APIs  
- Cards and funding  
- QR and transfers  
- Realtime telemetry  
- Admin and partner dashboards  

### 15.3 Pilot / Pre-Issuer Mode

Before issuer onboarding, MonCore may operate with:

- Open-banking funding  
- Card funding providers  
- Full AML and ledger enforcement  
- Full audit and reconciliation  

Issuer-dependent settlement remains contract-gated.

---

## 16. Threat Model & Trust Assumptions

MonCore assumes all external clients, networks, and providers may:

- Replay requests  
- Duplicate requests  
- Reorder delivery  

The kernel enforces:

- Authorization  
- Idempotency  
- Correlation  
- Ledger-first validation  

Before mutating financial state.

No external system is trusted to mutate balances, ledger entries, or reconciliation state directly.

---

## 17. Scalability & Throughput Model

MonCore is designed as a vertically consistent financial kernel with horizontally scalable API and execution layers.

Financial correctness is preserved independently of:

- Frontend concurrency  
- Provider throughput  
- Network retries  

Tenant and user workloads may be partitioned without violating:

- Ledger integrity  
- Snapshot consistency  
- Audit guarantees  

---

## 18. Regulatory Alignment

MonCore is designed to operate under:

- EMI regulation  
- PSD2 supervision  
- Card scheme requirements  
- Issuer technical governance  

The architecture supports:

- Regulator audits  
- Issuer certification  
- Scheme technical onboarding  
- Continuous supervisory access to audit and reconciliation data  

---

## 19. Out-of-Scope Interfaces

This document describes the financial kernel architecture only.

The following are intentionally excluded and documented separately:

- Public API definitions  
- UI applications  
- Partner dashboards  
- Tenant configurations  
- Product-specific workflows

This document describes conceptual architecture and control boundaries only. Internal data models, execution flows, and security mechanisms are intentionally not disclosed.

---

## 20. Security & Integrity Guarantees

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

## 21. Conclusion

MonCore is a financial operating system designed to provide issuer-grade correctness, compliance, and governance for modern regulated platforms.

By separating:

- Ledger authority  
- Compliance enforcement  
- Provider execution  

MonCore enables:

- Rapid deployment of regulated products  
- Provider independence  
- Regulator-grade auditability  
- Zero-migration issuer onboarding  

MonCore acts as a stable financial kernel capable of powering multiple regulated products under a single governed control plane.

