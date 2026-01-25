# 07 — Issuer Readiness & Transition Architecture
<div align="right">
← <a href="../00-doc-index.md">Back to Documentation Index</a>
</div>
 
## Public Architecture & Issuer Onboarding Document  

---

## 1. Purpose of This Document

This document describes how MonCore is architected to operate in an **issuer-agnostic, non-custodial mode** prior to sponsor onboarding, and how a licensed issuer and safeguarding institution can be integrated without platform refactoring, data migration, or ledger discontinuity.

The objective is to demonstrate that:

- MonCore never performs regulated custody, safeguarding, issuing, settlement, or scheme participation activities  
- All issuer-dependent execution paths are contract-gated and technically isolated  
- The financial core, ledger, reconciliation, dispute, and compliance layers are already built to issuer, scheme, and regulatory operational standards  
- Issuer onboarding activates pre-existing execution modules without modifying accounting state, ledger history, or transaction lineage  

This document is intended for:

- Issuer sponsors and BIN sponsors  
- Safeguarding institutions  
- Card schemes and scheme onboarding teams  
- Regulators and supervisory authorities  
- Platform partners and embedded finance operators  

---

## 2. Core Design Principle: Ledger Authority vs. Execution Authority

MonCore is architected around a strict and enforceable separation of responsibilities:

### Ledger Authority — MonCore (Technology Platform)

MonCore acts as the system of record and supervisory control plane for:

- User and tenant balances  
- Double-entry ledger integrity  
- Transaction lifecycle state  
- FX attribution and revenue accounting  
- Compliance enforcement and risk controls  
- Audit evidence, reconciliation state, and reporting  

MonCore:

- Does **not** hold client funds  
- Does **not** operate safeguarding accounts  
- Does **not** participate in card schemes  
- Does **not** clear or settle funds  
- Does **not** act as custodian or payment institution  

### Execution Authority — Licensed Issuer / Safeguarding Institution

The appointed issuer and safeguarding institution is exclusively responsible for:

- Custody and safeguarding of client funds  
- Scheme participation and card issuing  
- Settlement and clearing  
- External payouts and inbound transfers  
- Regulatory reporting and scheme compliance  

All regulated execution is performed exclusively by licensed counterparties.

MonCore operates as a **financial operating system and supervisory kernel**, never as a regulated financial institution.

---

## 3. Pre-Issuer Operation Mode (“Shadow Execution Mode”)

Before issuer onboarding, MonCore operates in a restricted, non-custodial execution model designed for audit-grade readiness:

- Ledger, transaction lifecycle, AML, FX, reconciliation, disputes, and settlement pipelines are fully implemented  
- Issuer connectors, transport adapters, and webhook processors are present but contract-gated and inactive  
- All balances are virtual and non-custodial  
- All issuer-dependent identifiers remain unbound  

In this mode:

- No real funds are held or moved  
- No scheme participation occurs  
- No safeguarding accounts are operated  

This enables:

- End-to-end technical validation of issuer flows  
- Regulator and issuer technical due diligence  
- Zero-risk verification of accounting, reconciliation, dispute handling, and compliance controls  

---

## 4. Issuer-Gated Activation Model

Issuer onboarding does not introduce new core modules.

Activation consists solely of:

- Provisioning issuer credentials per tenant and environment  
- Enabling pre-built execution connectors (cards, payouts, settlements, webhooks)  
- Binding issuer wallet identifiers to existing user and ledger records  

Controls already present in the platform:

- Per-tenant credential isolation  
- Sandbox / production environment separation  
- Contract-gated execution routing  
- Provider-scoped execution domains  
- Immutable ledger continuity  

There is:

- No ledger reset  
- No balance migration  
- No transaction rewriting  
- No historical data modification  

---

## 5. Card Issuing & Scheme Transaction Lifecycle Readiness

MonCore already implements a complete scheme-grade card lifecycle aligned with issuer and scheme operational models.

### Implemented Domains

- Card issuance registry (virtual and plastic)  
- Wallet tokenisation tracking (Apple Pay / Google Pay, scheme-mediated)  
- Authorisation event handling  
- Capture and reversal processing  
- Clearing and settlement staging  
- Chargebacks and dispute handling  

### Transaction Lifecycle Control

Each card transaction is processed through deterministic stages:

1. Scheme authorisation (no ledger movement)  
2. Authorisation recording and AML screening  
3. Capture confirmation (status only)  
4. Settlement webhook processing  
5. Ledger debit (idempotent, settlement-authoritative)  
6. Reconciliation and finalisation  
7. Chargeback / dispute handling (signed, idempotent, auditable)  

### Non-Repudiation & Evidence

All scheme and issuer events are protected using:

- Provider references and immutable identifiers  
- Payload hashing and cryptographic signatures  
- Idempotency guards and replay protection  
- Immutable audit logs  

This enables:

- Deterministic replay  
- Full scheme audit trails  
- Dispute reconstruction  
- Regulator-verifiable evidence exports  

---

## 6. Settlement & Clearing Architecture

MonCore implements a complete settlement and clearing control layer independent of any specific issuer.

### Core Pipelines

- Clearing staging tables  
- Settlement execution records  
- Adjustment and reversal handling  
- Provider reconciliation staging  
- Final settlement synchronisation  

### Guarantees

All settlement records are:

- Provider-scoped  
- Idempotent and replay-safe  
- Hash-sealed and cryptographically signed  
- Uniquely constrained  
- Fully auditable  

Ledger debits are executed only on settlement confirmation and never duplicated.

---

## 7. Daily Reconciliation & Safeguarding Alignment

MonCore implements daily reconciliation controls aligned with EMI and safeguarding regulatory requirements.

### Controls

- Issuer custody balance vs internal ledger comparison  
- Currency-level segregation  
- Precision-safe tolerance enforcement  
- Daily uniqueness constraints  
- Automatic mismatch expansion per user  

### Outputs

- Per-currency daily reconciliation reports  
- Difference calculation and classification  
- Resolution lifecycle tracking  
- Regulator-exportable audit trails  

This enables direct alignment between:

- Issuer safeguarding account balances  
- Internal ledger state  
- Regulatory reporting and supervisory review  

---

## 8. FX Execution & Revenue Attribution

MonCore implements a regulated FX attribution layer consistent with scheme and EMI expectations:

- Signed FX rate ingestion  
- Immutable FX audit logging  
- Per-transaction FX attribution  
- Spread revenue accounting  
- Settlement-authoritative FX reconciliation  

This ensures:

- Transparent conversion pricing  
- Regulator-verifiable FX provenance  
- Full separation between principal flows and revenue attribution  

---

## 9. Ledger Immutability & Balance Authority

MonCore operates a double-entry, immutable ledger with the following guarantees:

- Ledger entries cannot be deleted or rewritten  
- Unique transaction and idempotency enforcement  
- Snapshot-based balance materialisation  
- FX-aware multi-wallet booking model  
- Deterministic before / after balance tracking  

The ledger remains the sole authority for:

- User balances  
- Tenant exposure  
- Volume and turnover metrics  
- FX activity  
- Card issuance and transaction statistics  

Issuer onboarding never replaces or overrides ledger authority.

---

## 10. Compliance, AML & Supervisory Controls

Compliance is embedded directly into the financial kernel.

### Risk & AML

- Velocity monitoring and tier enforcement  
- Real-time AML screening  
- Case management and analyst decisions  
- Sanctions and PEP screening  
- Automatic account freezing and escalation  

### Audit & Supervision

- Immutable audit logs with hash chaining  
- Cryptographically signed system events  
- Full transaction lifecycle reconstruction  
- Partner and regulator supervision planes  

This enables:

- Regulator read-only dashboards  
- On-demand evidence export  
- Scheme, issuer, and supervisory audits  

---

## 11. Multi-Tenant Issuer Integration Model

Issuer onboarding is supported per tenant and per environment:

- Per-tenant issuer credentials  
- Sandbox / production isolation  
- Tenant-scoped execution routing  
- Tenant-scoped reconciliation and audit  
- Partner supervision workflows  

This enables:

- Multiple issuers per platform  
- Multiple products per issuer  
- Isolated regulatory boundaries between tenants  

---

## 12. Zero-Migration Transition Guarantee

Issuer onboarding requires:

- No data migration  
- No ledger reset  
- No transaction rewriting  
- No balance re-initialisation  

All historical data remains:

- Cryptographically intact  
- Regulator-verifiable  
- Scheme-auditable  

Activation consists only of binding:

- Issuer wallet identifiers  
- Provider references  
- Execution credentials  

---

## 13. Regulatory Positioning

MonCore operates strictly as:

- Technology platform operator  
- Ledger authority  
- Compliance enforcement system  

MonCore is explicitly **not**:

- An issuer  
- A safeguarding institution  
- A custodian  
- A scheme participant  
- A payment institution  

All regulated execution is performed exclusively by licensed counterparties under contractual appointment.

---

## 14. Conclusion

MonCore is architected as a true **Financial Operating System**:

- Issuer-agnostic by design  
- Regulator-grade before issuer onboarding  
- Zero-migration transition ready  
- Multi-tenant and multi-issuer capable  
- Fully auditable from day one  

Issuer onboarding activates execution — not architecture.

This enables rapid, compliant launch of regulated financial products without rebuilding the financial core.

