# Environment Parity — Sandbox = Production  
<div align="right">
← <a href="../00-doc-index.md">Back to Documentation Index</a>
</div>


--- 

## 1. Purpose of This Document

This document formally describes the environment parity model implemented within the MonCore Financial Operating System (“MonCore”).

It explains how MonCore enforces identical architecture, control paths, execution semantics, and audit guarantees across sandbox and production environments, while maintaining strict physical, logical, and legal isolation between environments.

This document is intended for:

- Issuer sponsors  
- Safeguarding institutions  
- Scheme partners  
- Regulatory reviewers  
- Platform partners  
- Fintech product builders  

The objective is to demonstrate that:

- Sandbox and production are architecturally identical  
- No sandbox-specific logic exists in the kernel  
- Regulated behavior can be validated before go-live  
- Transition to production requires no migration or product rewrite  

---

## 2. Environment Model Overview

MonCore is deployed as two fully independent but architecturally identical kernel instances:

- Sandbox environment  
- Production environment  

Each environment operates as a complete, self-contained MonCore deployment with its own:

- Databases  
- Provider credentials  
- API endpoints  
- Webhook receivers  
- Background job schedulers  
- Audit chains  

No environment shares:

- Storage  
- Credentials  
- Provider accounts  
- Network access  
- Ledger state  
- Audit history  

The two environments are physically, logically, and operationally isolated.

---

## 3. Environment Isolation Guarantees

MonCore enforces strict isolation between sandbox and production at all layers:

### 3.1 Database Isolation

Each environment operates on an independent database cluster.

- No shared schemas  
- No shared ledgers  
- No shared audit tables  
- No cross-environment queries  

Ledger state, balances, exposure counters, reconciliation records, and audit history are never replicated or synchronized between environments.

### 3.2 Credential & Identity Isolation

Each environment uses independent:

- API keys and tenant credentials  
- Partner identities  
- Admin identities  
- Provider signing keys  
- Webhook secrets  

Credentials issued in one environment are invalid in the other.

### 3.3 Provider & Counterparty Isolation

Each environment connects to separate provider accounts and endpoints:

- Sandbox providers and test schemes  
- Production providers and live schemes  

No provider account, issuer processor, or safeguarding account is shared across environments.

---

## 4. Code, Schema & Control Parity

MonCore enforces full code and schema parity between environments.

Characteristics:

- Single codebase  
- Identical binaries and container images  
- Identical database schemas and migrations  
- Identical triggers and constraints  
- Identical background jobs and schedulers  

There is:

- No sandbox-specific logic  
- No conditional execution paths by environment  
- No relaxed validation or reduced controls  

All financial flows, compliance checks, reconciliation jobs, and audit generation execute under identical logic in both environments.

Sandbox behavior is therefore production-equivalent by design.

---

## 5. Provider Credential & Endpoint Switching Model

Provider integrations are environment-agnostic.

The kernel executes the same:

- API calls  
- Idempotency logic  
- Webhook handling  
- Correlation propagation  
- Ledger posting  
- Audit logging  

In both environments.

Only the following differ between environments:

- Provider credentials  
- Provider endpoints  
- Scheme and issuer accounts  

This guarantees:

- Identical execution semantics  
- Identical failure modes  
- Identical reconciliation behavior  
- Identical audit lineage  

---

## 6. Turnkey Backend & Partner Integration Model

MonCore exposes a fully turnkey regulated backend.

Partners may integrate:

- Frontend-only applications  
- Backend-only systems  
- Hybrid products  

Without building:

- Ledger systems  
- Compliance engines  
- Reconciliation pipelines  
- Settlement tooling  
- Audit systems  

The MonCore kernel remains the:

- System of record  
- Compliance enforcement layer  
- Reconciliation authority  
- Audit evidence generator  

Partners receive only:

- Tenant credentials / API keys  
- Base API endpoint (sandbox or production)  

All regulated execution, compliance, and control remain centralized inside MonCore.

---

## 7. Partner Environment Transition Model

Transition from sandbox to production requires no technical migration.

Partners perform only:

- Replacement of API credentials  
- Change of base API endpoint  

There are:

- No API changes  
- No schema changes  
- No data migrations  
- No product rewrites  
- No flow changes  

All APIs, payloads, and execution semantics remain identical.

This guarantees:

- Stable partner integrations  
- Zero retraining  
- Zero product downtime  
- Zero re-certification risk  

---

## 8. Issuer-Gated Capability Activation

All issuer-dependent domains remain contract-gated in both environments:

- Card issuing  
- BIN sponsorship  
- Safeguarding  
- Settlement finalization  
- Scheme participation  

Before issuer onboarding:

- Issuer execution domains are inactive  
- Settlement remains in shadow / pilot mode  
- Custody and safeguarding remain external  

After onboarding:

- Capabilities are activated by contract  
- The same kernel, ledger, users, and audit history continue without migration  

No environment transition is required for issuer activation.

---

## 9. Background Jobs & Enforcement Parity

All system control and enforcement jobs execute identically in both environments:

- AML continuous sweeps  
- Velocity and exposure enforcement  
- Geo-risk controls  
- Card lifecycle enforcement  
- Reconciliation schedulers  
- Audit chain integrity validation  

All jobs are:

- Idempotent  
- Advisory-lock protected  
- Fully audit-logged  
- Regulator-inspectable  

No enforcement behavior differs between sandbox and production.

---

## 10. Sandbox → Production Transition

Production deployment is performed as a new MonCore instance:

- New infrastructure  
- New database  
- New credentials  
- Same codebase  
- Same schema  
- Same migrations  
- Same background jobs  

Sandbox data is not migrated.

Production begins with a clean ledger while preserving identical execution semantics.

This enables:

- Independent regulatory testing  
- Clean issuer onboarding  
- Continuous audit lineage  
- Zero cross-environment contamination  

---

## 11. Regulatory & Issuer Alignment

This deployment model satisfies:

- EMI technical due diligence  
- Sponsor bank onboarding requirements  
- Scheme certification expectations  
- Supervisory sandbox validation  
- Operational resilience guidelines  

Regulators and issuers may:

- Audit sandbox behavior as production-equivalent  
- Validate reconciliation and audit paths before go-live  
- Perform supervised walkthroughs on real flows  

---

## 12. Conclusion

MonCore enforces strict environment parity by design.

Sandbox and production are:

- Architecturally identical  
- Behaviorally identical  
- Control-equivalent  
- Audit-equivalent  

While remaining:

- Physically isolated  
- Legally separated  
- Operationally independent  

This guarantees:

- Safe pre-issuer pilots  
- Regulator-grade validation  
- Zero-migration issuer onboarding  
- Stable long-term production operation  

MonCore therefore provides a production-grade financial kernel whose behavior can be fully validated before any regulated go-live.

---

