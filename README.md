# MonCore — Financial Operating System
<div align="right">
<a href="/docs/00-doc-index.md">Go to Documentation Index →</a>
</div>

---

## MonCore is a regulator-grade **financial operating system** architected as a ledger-first financial kernel for issuer-led banking, payments, and embedded finance.

Plug-and-Play Regulated Product Backend

MonCore provides a full plug-and-play regulated product backend.

Products built on MonCore do not integrate authentication, onboarding, compliance, payments, cards, reconciliation, or settlement systems independently.

All regulated domains — identity, AML, security, payments, cards, FX, reconciliation, audit and dashboards — are pre-integrated and governed inside the MonCore kernel.

Partners integrate with MonCore through governed platform APIs or tenant-scoped frontend integration, and can deploy regulated financial products without building execution, security, or compliance stacks.

→ See: Platform Overview – Plug-and-Play Product Backend

It operates as the **authoritative system of record** for balances, compliance enforcement, reconciliation, and audit evidence — while keeping issuing, safeguarding, settlement, and scheme participation **strictly issuer-gated and contract-controlled**.

MonCore is designed for:

- Issuer and sponsor banks  
- Electronic Money Institutions (EMIs)  
- Card issuers and scheme programmes  
- Regulated fintech platforms  
- Supervisory and audit review  

MonCore is not a consumer product and not a bank.  
It is a **financial kernel and operating framework** for building and supervising regulated financial systems.

---

## Architectural Positioning

MonCore implements a strict separation between:

- **Ledger authority and balance integrity**  
- **Compliance and risk enforcement**  
- **External execution, custody, and settlement providers**  

The kernel remains the single authority for all regulated state.

External providers act strictly as execution agents and never mutate balances, ledger state, exposure counters, or reconciliation records.

All issuer-dependent capabilities remain **inactive until formal onboarding and contract activation**.

---

## Core Design Principles

MonCore is built around non-negotiable regulatory and architectural guarantees.

### Ledger-First Kernel  
All monetary state is derived exclusively from an immutable double-entry ledger.  
Balances are never authoritative and are always computed from ledger history or validated snapshots.

### System-of-Record Authority  
No execution provider, processor, or partner may mutate balances, ledger state, or compliance state.  
All regulated mutations are adjudicated exclusively by the MonCore kernel.

### Compliance-Native Architecture  
AML / CTF evaluation, velocity enforcement, tier limits, freezing, reconciliation audits, and export evidence are executed inline with financial posting.

Compliance is not an external system — it is embedded inside the kernel.

### Issuer-Gated Execution  
Issuing, BIN sponsorship, safeguarding, withdrawals, and settlement finalization remain **contract-gated and inactive** until issuer onboarding is completed.

### Multi-Tenant by Design  
Multiple regulated programmes may operate on a single kernel with:

- Strict tenant isolation  
- Jurisdiction gating  
- Capability-based activation  
- Independent partner governance  

### Sandbox = Production  
The same code, schema, controls, background jobs, reconciliation logic, and audit paths run in both sandbox and production.

There are:

- No sandbox shortcuts  
- No reduced controls  
- No environment-specific logic  

Only credentials and counterparties differ.

---

## What MonCore Is

- A **financial operating system** for regulated deployment  
- A **ledger-authoritative kernel** for multi-product platforms  
- A **compliance-native core** for issuer-led banking and embedded finance  
- A **regulator-reviewable system** with full forensic and audit integrity  

## What MonCore Is Not

- Not a neobank application  
- Not a payments API wrapper  
- Not a UI-first core banking product  
- Not a sandbox prototype  
- Not a custody, issuing, or settlement provider  

MonCore does not hold client funds, issue cards, or perform settlement without an onboarded issuer sponsor.

---

## Regulatory & Issuer Readiness

MonCore is architected to satisfy and exceed expectations for:

- EMI and sponsor-bank technical due diligence  
- Issuer and scheme onboarding review  
- Safeguarding separation and non-delegation  
- Audit logging and forensic reconstruction  
- Supplier risk and operational resilience  
- Supervisory sandbox validation  

All critical subsystems enforce:

- Immutable ledger and audit chains  
- Deterministic execution and idempotency  
- Fail-closed financial behavior  
- Cryptographic evidence integrity  
- Daily reconciliation readiness  

---

## Pre-Issuer Pilot & Shadow Mode Operation

MonCore is designed to operate in a regulated **pre-issuer pilot / shadow mode**.

In this mode:

- Execution is performed by licensed Tier-1 providers  
- Issuer-dependent domains remain contract-gated  
- The full ledger, compliance, idempotency, and audit kernel is active  

Current pilot-ready capabilities include:

- Open-banking funding and withdrawals  
- Card-based top-ups and funding flows  
- Full KYC / KYB / UBO onboarding  
- Embedded AML and velocity enforcement  
- Multi-currency wallets and internal transfers  
- Regulator-grade audit and reconciliation trails  

When an issuer sponsor is onboarded:

- Issuing, safeguarding, and settlement are activated by contract  
- Existing users, balances, and ledger state continue unchanged  
- No migration, rewrite, or downtime is required  

This enables **zero-migration transition from pilot to regulated production**.

---

## Repository Scope

This public repository contains **documentation only**.

It intentionally does NOT contain:

- Production code  
- Database schemas  
- Configuration files  
- Secrets or credentials  
- Execution workflows  

Published materials include:

- Sanitized architecture documentation  
- Regulatory positioning documents  
- Trust boundary and isolation models  
- Environment parity statements  

All sensitive technical materials, diagrams, walkthroughs, and sandbox access are provided **only under NDA** to regulated institutions.

---

## Public Documentation Index

The following documents are publicly available and sanitized for open distribution:

- **[Documentation Pack – Public Index](docs/00-doc-index.md)**

- **Platform Overview**  
  [`01-platform-overview.md`](./docs/public/01-platform-overview.md)

- **System Architecture — MonCore Kernel**  
  [`02-system-architecture.md`](./docs/public/02-system-architecture.md)

- **Data Flow & Trust Boundaries**  
  [`03-data-flow-and-trust-boundaries.md`](./docs/public/03-data-flow-and-trust-boundaries.md)

- **Environment Parity — Sandbox = Production**  
  [`04-environment-parity.md`](./docs/public/04-environment-parity.md)

- **Multi-Tenant Architecture & Isolation Controls**                              
  [`05-multi-tenant-and-isolation.md`](./docs/public/05-multi-tenant-and-isolation.md)

- **Provider Abstraction & Execution Framework**                      
  [`06-provider-abstraction-and-execution.md`](./docs/public/06-provider-abstraction-and-execution.md)

These documents describe MonCore’s operating model and regulatory architecture at a non-confidential level.

A full regulator-grade documentation pack is maintained privately and provided only under formal engagement.

---

## Access & Engagement

This repository is intended for:

- Issuer banks and safeguarding institutions  
- Electronic Money Institutions (EMIs)  
- BIN sponsors and card issuers  
- Regulated fintech platforms  
- Supervisory and audit bodies  

For technical access, documentation packs, or sandbox walkthroughs:

- General & institutional: **contact@moncore.eu**  
- Partnerships & issuer onboarding: **partnerships@moncore.eu**  
- Compliance & regulatory: **compliance@moncore.eu**  

Please include:

- Institution  
- Role  
- Jurisdiction  
- Evaluation scope  

---

## Disclosure & Security

This repository contains controlled architectural material.

- No confidential information may be redistributed without written authorization  
- Security issues must be reported privately via the compliance channel  
- No production behavior differs between sandbox and live environments  

---

## Maintainer & Founder

MonCore is designed and maintained by:

**Mon Florin**  
Founder & Financial Systems Architect  
Italy / European Union  

GitHub: https://github.com/MonFlorin  
Email: florinmon@proton.me  

MonCore is developed as a long-term regulated financial infrastructure project.

---

**MonCore — Financial Operating System**  
Ledger-authoritative. Compliance-native. Issuer-led by design. infrastructure project.
