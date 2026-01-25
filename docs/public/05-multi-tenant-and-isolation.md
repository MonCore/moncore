# Multi-Tenant Architecture & Isolation Controls  
<div align="right">
← <a href="../00-doc-index.md">Back to Documentation Index</a>
</div>

---

## 1. Purpose of This Document

This document describes how MonCore enforces **strict multi-tenant isolation** across application, runtime, and database layers in order to safely operate multiple regulated programmes on a single financial operating system.

It defines the technical and governance controls that ensure:

- Complete segregation of tenant data and execution scope  
- Deterministic prevention of cross-tenant access  
- Regulator-grade auditability and lifecycle enforcement  
- Controlled onboarding, suspension, and termination of tenant programmes  

This document is intended for:

- Issuing and safeguarding institutions  
- Scheme sponsors and BIN sponsors  
- External auditors and supervisory authorities  
- Partners onboarding regulated pilot programmes  
- Internal compliance, risk, and operations stakeholders  

This document is **provider-agnostic** and describes MonCore platform controls independently of any specific issuer, processor, or banking partner.

---

## 2. Tenant Definition and Isolation Objectives

### 2.1 Tenant Definition

A **tenant** represents an isolated regulated programme, partner product, or pilot deployment operating on MonCore.

Each tenant has its own:

- Configuration and operational metadata  
- API credentials and execution environments  
- Users, wallets, cards, and ledger records  
- Partner operators and authenticated sessions  
- Audit and lifecycle history  

### 2.2 Isolation Objectives

MonCore enforces the following isolation guarantees:

- No tenant can access another tenant’s users, balances, cards, ledger, or compliance data  
- Partner operators are restricted exclusively to their assigned tenant  
- Internal administrators operate under explicit role-based authority  
- Tenant lifecycle actions are auditable and irreversible when required  
- All financial exposure is computed from tenant-scoped ledger data  

Isolation is enforced through a layered model:

- API-level tenant context attachment  
- Runtime guards and capability gating  
- Database-anchored tenant ownership and referential controls  
- Immutable audit and ledger enforcement  

---

## 3. Tenant Context Attachment (API Layer)

All tenant-scoped requests operate under a mandatory tenant context.

### 3.1 Tenant Identification

Tenant identity is extracted from authenticated request headers and attached to the execution context.

Properties:

- Tenant identity is required for all tenant-scoped routes  
- Missing tenant context is rejected  
- Unknown, suspended, or terminated tenants are blocked  

### 3.2 Tenant Guard Enforcement

Before execution:

- Tenant existence is validated  
- Tenant status is enforced (`ACTIVE`, `SUSPENDED`, `TERMINATED`)  
- Tenant operational metadata is attached for downstream enforcement  

This prevents silent tenant ambiguity and guarantees deterministic execution scope.

Internal administrative routes are system-scoped and explicitly excluded from tenant header influence.

---

## 4. Runtime Capability and Jurisdiction Gating

Tenant configuration is meta-driven and enforced at runtime.

### 4.1 Service Enablement Gates

Each regulated capability (cards, transfers, FX, safeguarding, open-banking, QR, reconciliation) is explicitly enabled per tenant.

Controls:

- Services must be explicitly configured per tenant  
- Disabled services are rejected at runtime  
- Capability changes emit auditable tenant lifecycle events  

This enables staged onboarding, controlled pilot activation, and regulated production rollout.

### 4.2 Jurisdiction Enforcement

Tenants are restricted to configured jurisdictions.

Controls:

- Jurisdiction must be explicitly authorised  
- Out-of-scope jurisdictions are rejected  
- Prevents regulatory deployment drift across territories  

---

## 5. Database-Level Tenant Anchoring (Primary Isolation Layer)

MonCore enforces tenancy primarily at the database level.

### 5.1 Tenant Anchoring Model

Tenant ownership is anchored at the user layer:

- Every user record contains a mandatory `tenant_id`  
- All financial, card, AML, QR, reconciliation, and compliance records reference users  
- Tenant identity is therefore inherited transitively through enforced joins  

Examples:

- Ledger entries → user → tenant  
- Cards → user → tenant  
- AML cases and events → user → tenant  
- Partner sessions and actions → tenant  
- Exposure and compliance views → tenant  

This ensures:

**No financial or compliance record exists without deterministic tenant ownership.**

### 5.2 Referential Integrity Controls

Tenant isolation is reinforced through enforced referential constraints and join-anchored ownership:

- `users.tenant_id` references tenants with `ON DELETE RESTRICT`  
- Partner users reference tenants  
- Tenant credentials reference tenants  
- Tenant audit events reference tenants  

These controls prevent:

- Orphaned financial or compliance records  
- Cross-tenant reassignment  
- Accidental tenant detachment  

---

## 6. Ledger Immutability and Financial Record Protection

MonCore enforces an append-only financial ledger model at database level.

### 6.1 Ledger Deletion Prevention

Ledger rows are protected by database triggers that:

- Block all delete operations  
- Raise hard exceptions on any deletion attempt  

This guarantees:

- Financial history cannot be erased  
- Reconciliation and audit trails remain intact  
- Regulatory evidence remains immutable  

Application-level controls and audit enforcement prevent unauthorised mutation.

### 6.2 Transaction Uniqueness

Ledger records are protected by:

- Unique `(tx_id, user_id, currency)` constraints  
- Unique transaction identifiers  
- Unique provider and charge references  

This prevents:

- Duplicate postings  
- Replay or double settlement  
- Cross-tenant transaction collision  

---

## 7. Export and Reporting Immutability

All regulator and operator exports are protected by immutable audit controls.

### 7.1 Export Audit Immutability

Export audit records are protected by:

- Database triggers preventing update or delete  
- Hard exceptions on any mutation attempt  

Each export records:

- Administrator identity and role  
- Export scope and export type  
- Row counts  
- Cryptographic file hash (SHA-256)  
- Timestamp, IP address, and environment  

This guarantees:

**All regulatory exports are tamper-proof and forensically verifiable.**

---

## 8. Tenant Financial Exposure and Safeguarding Reporting

MonCore computes tenant exposure using deterministic, ledger-only SQL views.

### 8.1 Ledger-Only Exposure Model

Tenant exposure is derived exclusively from:

- Immutable ledger entries  
- User-anchored tenant ownership  
- Authoritative FX rates at execution time  

Computed metrics:

- Total tenant balance (EUR normalised)  
- Month-to-date inflow volume  
- Month-to-date FX volume (multi-currency transactions only)  
- Total active cards issued  

Design principles:

- No application-layer aggregation  
- No cached or inferred balances  
- Transfers neutralised at transaction-pair level  
- FX counted only when currencies differ  

This enables:

- Safeguarding balance verification  
- Issuer exposure review  
- Regulator-grade balance confirmation  

---

## 9. Partner Isolation and Delegated Operator Controls

Partners operate under strict tenant-scoped authentication and authorisation.

### 9.1 Partner Authentication

Partner operators authenticate using:

- Tenant-bound partner user accounts  
- Tenant-bound session tokens  
- Expiry-enforced sessions  
- Role-restricted permissions  

Attached execution scope:

- Partner identity  
- Tenant identity  
- Operator role  

### 9.2 Partner Ledger and Action Isolation

All partner access enforces:

- Tenant scope derived exclusively from authenticated session  
- Tenant identifiers are never accepted from query parameters  
- Ledger and compliance queries join through user → tenant ownership  

Partner action requests enforce:

- Partner + tenant ownership  
- User ownership within the same tenant  
- Status-controlled approval lifecycle  

This prevents:

- Cross-tenant visibility  
- Partner privilege escalation  
- Indirect tenant enumeration  

---

## 10. Internal Administration Isolation and Governance

Internal administrators operate under a system-scoped control plane.

### 10.1 Internal Admin Authentication

Controls:

- Token-based sessions with hashing  
- Expiry enforcement  
- Active status verification  
- Role-based permission gates  

### 10.2 Tenant-Agnostic Oversight

Internal admin routes are:

- Explicitly excluded from tenant header influence  
- Role-restricted by function (`SUPER_ADMIN`, `COMPLIANCE`, `OPS`, `AUDITOR`)  
- Fully auditable  

This enables:

- Cross-tenant supervisory review  
- Issuer and regulator access models  
- Controlled lifecycle and intervention operations  

---

## 11. Tenant Lifecycle Controls

Tenants operate under a strict lifecycle:

- `ACTIVE`  
- `SUSPENDED`  
- `TERMINATED`  

### 11.1 Suspension

Suspension:

- Immediately blocks tenant execution  
- Preserves all data and audit history  
- Emits critical tenant audit events  

Used for:

- Compliance intervention  
- Issuer instruction  
- Regulatory enforcement  

### 11.2 Termination

Termination:

- Is irreversible  
- Records termination timestamps and reasons  
- Emits critical tenant audit events  
- Permanently blocks tenant execution  

---

## 12. Compliance and Regulator Views

MonCore exposes tenant-scoped regulatory supervision views.

### 12.1 Compliance Transaction View

Regulatory transaction views:

- Join ledger → users → tenants  
- Exclude reversed records  
- Include provider, direction, status, and timestamps  

This enables:

- Per-tenant transaction supervision  
- Scheme and issuer reconciliation  
- AML, sanctions, and fraud review  

### 12.2 Unified Compliance Timeline

Compliance timelines unify:

- AML events and case activity  
- KYC / KYB decisions  
- Sanctions and fraud flags  
- Card, settlement, and ledger events  

Tenant identity is derived from:

- Explicit payload references, or  
- User tenant ownership  

This produces a deterministic, tenant-scoped regulatory event stream.

---

## 13. Audit Evidence and Integrity Guarantees

MonCore enforces write-only audit and forensic integrity patterns.

### 13.1 Audit Log Integrity

Controls:

- Unique payload hash constraints  
- Provider + payload uniqueness  
- High-granularity indexing for forensic queries  

Audit records include:

- Actor identity  
- Module and action  
- Result and severity  
- Hash-based integrity fields  

This supports replay detection, forensic reconstruction, and regulatory verification.

### 13.2 Tenant Audit Events

Tenant lifecycle actions generate:

- Dedicated tenant audit events  
- Tenant-bound foreign keys  
- Time-ordered, immutable history  

This supports:

- Issuer review  
- Regulatory inspection  
- Incident reconstruction  

---

## 14. Indexing, Performance, and Supervised Review

MonCore provides extensive tenant-aware indexing for:

- Tenant filtering and lifecycle review  
- Ledger reconstruction  
- AML supervision  
- Partner oversight  
- Export traceability  

Indexes are optimised for:

- Time-ordered forensic review  
- Regulator batch exports  
- High-volume reconciliation pipelines  
- Transaction supervision at scale  

---

## 15. Threat Model and Mitigations

### 15.1 Cross-Tenant Data Access Attempt

Mitigations:

- Mandatory tenant anchoring via users  
- Tenant guard validation  
- Partner tenant-bound sessions  
- Join-based tenant enforcement at database level  

### 15.2 Ledger or Export Tampering

Mitigations:

- Ledger deletion triggers block erasure  
- Export audit triggers block mutation  
- Unique transaction and payload constraints prevent replay  

### 15.3 Privilege Escalation

Mitigations:

- Partner roles tenant-scoped  
- Internal admin roles explicitly restricted  
- Lifecycle hard stops enforced  

---

## 16. Onboarding and Verification Checklist

For each new tenant:

- Tenant record created with lifecycle state `ACTIVE`  
- Tenant operational metadata configured  
- Tenant credentials issued per environment  
- Partner operators bound to tenant  
- Capability and jurisdiction gates configured  

Audit verification:

- Tenant creation event present  
- Capability updates audited  
- Suspension / termination events traceable  

Exposure verification:

- Ledger-derived tenant exposure view reconciles correctly  

---

## 17. Conclusion

MonCore enforces multi-tenant isolation using a defence-in-depth architecture:

- Deterministic tenant context attachment  
- Runtime capability and jurisdiction enforcement  
- Database-anchored tenant ownership  
- Immutable ledger and export audit controls  
- Role-scoped partner and administrator governance  
- Regulator-grade exposure and compliance views  

This architecture enables MonCore to safely operate:

- Multiple regulated programmes  
- Multiple issuing and safeguarding relationships  
- Multi-jurisdiction deployments  

while preserving:

- Confidentiality between tenants  
- Integrity of financial records  
- Supervisory transparency  
- Issuer and regulator confidence  


