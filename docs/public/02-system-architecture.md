## System Architecture — MonCore Financial Operating System


## 1. Overview

MonCore is a production-grade financial operating system designed to act as the core execution and control layer for regulated digital money, card, and payment platforms.

MonCore is not a consumer application and not a bank.

It is a multi-tenant financial kernel that provides:

Canonical ledger and balance authority  
Embedded compliance and governance  
Deterministic transaction execution  
Reconciliation and settlement control  
Provider-agnostic execution interfaces  

The platform is designed for issuer-led deployment under EMI, PSD2, card scheme, and bank supervision, with strict separation between internal financial state and external execution providers.

---

## 2. High-Level Architecture

MonCore is structured as three strictly separated planes:

---
┌──────────────────────────────────────────────┐
│                Product Plane                 │
│  (Wallets, Cards, QR, Transfers, Dashboards) │
└──────────────────────────────────────────────┘
                     │
                     ▼
                     
┌──────────────────────────────────────────────┐
│        MonCore Kernel (Control Plane)        │
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
│               Execution Plane                │
│  (Issuers, Safeguarding, Cards, Open Banking │
│   FX, Identity, Payments)                    │
└──────────────────────────────────────────────┘

---
Only the kernel is authoritative.

External providers act strictly as execution agents and never hold authority over ledger state, balances, compliance state, or reconciliation outcomes.

---

## 3. Data Custody & Legal Boundary

MonCore does not act as:

Issuer  
Safeguarding institution  
Settlement agent  
Payment institution  

All legal custody, safeguarding, and settlement remain the responsibility of licensed execution institutions.

MonCore maintains internal representations of:

Balances  
Compliance state  
Exposure state  
Audit and reconciliation evidence  

MonCore acts exclusively as a regulated system-of-record and control platform and does not itself hold client funds.

---

## 4. Core Ledger & Balance Architecture


### 4.1 Canonical Ledger

All monetary state is derived exclusively from the canonical ledger.

The canonical ledger is implemented in the ledger_entries table.

Every financial operation generates one or more immutable ledger entries containing:

Transaction identifier  
User and tenant ownership  
Currency and signed amount  
Execution provider reference  
FX attribution (when applicable)  
Status lifecycle (CREATED, AUTHORIZED, SETTLED, REVERSED)  

Ledger rows are protected against deletion by database-level constraints and triggers.

Uniqueness constraints enforce:

One posting per transaction / user / currency  
One settlement per provider reference  
No duplicate charge identifiers  

This enforces:

No double posting  
No duplicate settlement  
Deterministic replay safety  

The auxiliary ledger table exists for reporting and compatibility purposes and is not authoritative.


### 4.2 Balance Snapshots

Authoritative balances are always derived from the ledger.

Balance snapshots provide controlled materialized views per:

User  
Currency  

Snapshots are regenerated exclusively from ledger aggregation and protected by uniqueness and integrity constraints.

Snapshots are a performance layer only.

The ledger remains the single source of truth.

---

## 5. Disaster Recovery & State Reconstruction

Because all balances and exposure are derived from immutable ledger postings, MonCore supports forensic reconstruction of:

Balances  
Exposure  
Transaction lifecycles  

Settlement lineage is reconstructed from the ledger plus settlement and reconciliation evidence tables.

No in-memory or ephemeral financial state is authoritative.

This enables:

Full balance recovery  
Settlement re-derivation  
Audit replay  
Regulator forensic review  

After system failure, corruption, or operator error, the complete financial state can be reconstructed deterministically from ledger history and reconciliation evidence.

---

## 6. Multi-Tenant Isolation Model

MonCore supports multiple regulated tenants operating on the same kernel.

Isolation is enforced by:

Tenant ownership on all user records  
Tenant-scoped wallets and counters  
Tenant-scoped credentials and API keys  
Dedicated partner and admin audit planes  

Tenants cannot observe or influence any other tenant’s data.

All aggregation views operate strictly through tenant-scoped joins.

All regulated execution domains remain disabled until explicitly activated by contract and jurisdiction.

---

## 7. Deterministic Execution & Idempotency

All financial requests are protected by kernel-level idempotency.

The idempotency layer enforces:

One execution per logical request  
Verified response replay  

Correlation identifiers are persisted in the idempotency layer and audit/event payloads; ledger entries are linked through transaction identifiers and provider references.

This enforces:

Exactly-once posting  
Safe retries  
No double debit or credit under webhook duplication or provider replay  

---

## 8. Embedded Compliance & Risk Control

Compliance is embedded directly into the kernel.

Native subsystems include:

AML event logging  
Velocity and rolling counters  
Case management and decisions  
Sanctions and PEP datasets and enforcement hooks  
Geographic restrictions and account freezing  

Before any ledger posting is executed:

Velocity counters are evaluated  
Tier limits are enforced  
AML risk is assessed  

Blocked or flagged transactions are rejected in the execution pipeline before posting to the ledger.

---

## 9. Exposure & Regulatory Counters

MonCore computes regulatory exposure directly from the ledger.

The counters subsystem records:

Per-user rolling volumes  
Per-product regulatory counters  
Timestamped velocity windows  

Tenant-level exposure provides:

Total ledger balances  
Monthly transaction volume  
Monthly FX volume  
Total cards issued  

All exposure metrics are ledger-derived and provider-independent.

---

## 10. Provider-Agnostic Execution Layer

External providers are integrated behind stable kernel interfaces.

Execution domains include:

Open-banking funding  
Card funding  
Card authorisation and clearing  
Card settlement  
Bank withdrawals  

Each provider interaction is:

Idempotent  
Correlated  
Audited  
Reconciled back into the ledger  

Providers may be integrated or replaced without modifying:

Ledger model  
Compliance model  
Reconciliation logic  
Audit exports  

---

## 11. Reconciliation & Settlement Control

MonCore implements a native settlement and reconciliation engine.

Settlement records include:

Provider reference  
Amount and currency  
Canonical payload hash  
System signature  
Processing timestamps  

Daily reconciliation produces:

Issuer versus backend balance reports  
Exception mismatch records  
Settlement adjustment records  

This enforces:

No double settlement  
No missing settlement  
Cryptographically verifiable settlement lineage  

---

## 12. Audit & Evidence Layer

All critical operations generate append-only audit records.

Audit events include:

Financial intents and results  
Provider callbacks  
Compliance decisions  
Admin and partner actions  
Identity lifecycle events  

Integrity is enforced through:

Payload hashing  
Provider and payload uniqueness  
Export immutability controls  

Export audit logs are protected by database-level immutability.

Derived regulator-safe views include:

Admin actions timeline  
Compliance timeline  
Transaction lifecycle views  
Compliance transaction reports  

All exports are tracked with:

File hashes  
Row counts  
Time scopes  
Operator identity  

---

## 13. Tenant, Partner & Admin Control Planes

MonCore separates three governance planes.


### 13.1 Product Plane

End-user operations:

Wallets and balances  
Transfers and QR payments  
Cards and funding  
Authentication and sessions  


### 13.2 Partner / Tenant Plane

Tenant operators access:

Tenant dashboards  
Compliance timelines  
Ledger and exposure exports  
Partner audit trails  

All partner actions are fully audited.


### 13.3 Internal Admin Plane

Internal governance is isolated through:

Dedicated admin identities  
Segmented sessions  
Immutable admin audit trails  

Internal admin tooling is designed for regulator-safe review and evidence export; financial state mutation is restricted to controlled operational flows.

---

## 14. Sandbox = Production Parity

MonCore enforces full environment parity.

The same:

Ledger model  
Compliance rules  
Counters  
Reconciliation jobs  
Audit paths  

Run in both sandbox and production.

Only the following differ:

Credentials  
Counterparties  
Settlement endpoints  

This enables:

Pre-issuer sandbox audits  
Regulator walkthroughs  
Pilot deployments without migrations  

---

## 15. Supported Deployment Models


### 15.1 Kernel-Only Integration

MonCore provides:

Ledger  
Compliance  
Reconciliation  
Exposure  

The partner supplies all frontend and orchestration.


### 15.2 Full Stack Platform

MonCore provides:

Wallet APIs  
Cards and funding  
QR and transfers  
Realtime telemetry  
Admin and partner dashboards  


### 15.3 Pilot / Pre-Issuer Mode

Before issuer onboarding, MonCore may operate with:

Open-banking funding  
Card funding providers  
Full AML and ledger enforcement  
Full audit and reconciliation  

Issuer-dependent settlement remains contract-gated.

---

## 16. Threat Model & Trust Assumptions

MonCore assumes all external clients, networks, and providers may:

Replay requests  
Duplicate requests  
Reorder delivery  

The kernel enforces:

Authorization  
Idempotency  
Correlation  
Ledger-first validation  

Before mutating any financial state.

No external system is trusted to mutate balances, ledger entries, or reconciliation state directly.

---

## 17. Scalability & Throughput Model

MonCore is designed as a vertically consistent financial kernel with horizontally scalable API and execution layers.

Financial correctness is preserved independently of:

Frontend concurrency  
Provider throughput  
Network retries  

Tenant and user workloads may be partitioned without violating:

Ledger integrity  
Snapshot consistency  
Audit guarantees  

---

## 18. Regulatory Alignment

MonCore is designed to operate under:

EMI regulation  
PSD2 supervision  
Card scheme requirements  
Issuer technical governance  

The architecture supports:

Regulator audits  
Issuer certification  
Scheme technical onboarding  
Continuous supervisory access to audit and reconciliation data  

---

## 19. Out-of-Scope Interfaces

This document describes the financial kernel architecture only.

The following are intentionally excluded and documented separately:

Public API definitions  
UI applications  
Partner dashboards  
Tenant configurations  
Product-specific workflows  

This document describes conceptual architecture and control boundaries only. Internal data models, execution flows, and security mechanisms are intentionally not disclosed.

---

## 20. Security & Integrity Guarantees

MonCore provides:

Immutable canonical ledger  
Deterministic idempotency  
Snapshot derivation  
Payload hashing  
System signatures  
Full audit lineage  

The kernel enforces:

No balance desynchronisation  
No double debit or credit  
No settlement duplication  
Full forensic traceability  

---

## 21. Conclusion

MonCore is a financial operating system designed to provide issuer-grade correctness, compliance, and governance for modern regulated platforms.

By separating:

Ledger authority  
Compliance enforcement  
Provider execution  

MonCore enables:

Rapid deployment of regulated products  
Provider independence  
Regulator-grade auditability  
Zero-migration issuer onboarding  

MonCore acts as a stable financial kernel capable of powering multiple regulated products under a single governed control plane.
