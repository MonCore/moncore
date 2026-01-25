# MonCore — Documentation Index

This repository contains sanitized public documentation and a public index of the regulator-grade private documentation pack maintained for the 
**MonCore - Financial Operating System.**

Its purpose is to provide issuer sponsors, EMIs, sponsor banks, regulated partners, technical evaluators, and supervisory reviewers with a transparent view of MonCore’s architecture principles, operating model, and regulatory readiness — without disclosing confidential implementation details or proprietary controls.

---

## Public Documentation (Sanitized)

The following documents are publicly available for open distribution:

- **Platform Overview**  
  [`01-platform-overview.md`](./public/01-platform-overview.md)

- **System Architecture — MonCore Kernel**  
  [`02-system-architecture.md`](./public/02-system-architecture.md)

- **Data Flow & Trust Boundaries**  
  [`03-data-flow-and-trust-boundaries.md`](./public/03-data-flow-and-trust-boundaries.md)

- **Environment Parity — Sandbox = Production**  
  [`04-environment-parity.md`](./public/04-environment-parity.md)

- **Multi-Tenant Architecture & Isolation Controls**                              
  [` 05-multi-tenant-and-isolation.md`](./public/05-multi-tenant-and-isolation.md)
  
- **Provider Abstraction & Execution Framework**                      
  [`06-provider-abstraction-and-execution.md`](./public/06-provider-abstraction-and-execution.md)
  
These documents describe MonCore’s operating model, control boundaries, and regulatory positioning at a **non-confidential** level.

---

## Private Documentation Pack (Public Index Only)

All documents listed below exist in a controlled private pack and are provided **only** to regulated counterparties under NDA and formal engagement.

The full documentation set is prepared for:

- Issuer onboarding and technical certification  
- EMI and sponsor-bank due diligence  
- Scheme participation and programme governance  
- Safeguarding review and separation controls  
- Supervisory inspection and audit review  

### 1) Core System & Architecture (Foundation)
- System Architecture — MonCore Kernel  
- Data Flow & Trust Boundaries  
- Environment Parity Statement (Sandbox = Production)  
- Ledger Architecture & Balance Integrity  

### 2) Financial & Safeguarding Framework
- Safeguarding of Client Funds  
- Settlement & Reconciliation Framework  

### 3) Compliance & Risk Control
- AML / CTF Policy  
- Transaction Monitoring Framework  
- Account Controls & Freeze Enforcement  
- Audit Logging & Evidence Integrity  
- Idempotency & Financial Safety Controls  

### 4) Operations & Platform Reliability
- Operational Monitoring & Telemetry  
- Background Jobs & Deterministic Processing  
- Security Events & Incident Detection  

### 5) Authentication & Session Governance
- Authentication & Sessions  

### 6) Multi-Tenant & Governance
- Tenant & Multi-Entity Architecture  
- Admin & Compliance Tools  

### 7) Third-Party & Supplier Risk
- Third-Party Providers & Responsibilities  
- Supplier Risk & Dependency Management  

### 8) Readiness & Regulatory Operations
- Sandbox Readiness Statement  
- Production Readiness Roadmap  
- Incident Response & Regulatory Notification Policy  
- Business Continuity & Disaster Recovery Policy  
- Data Protection & Privacy Governance Policy  

---

## Repository Scope & Disclosure

This public repository contains **documentation only**.

It does not include:
- production code, schemas, secrets, configuration, or live execution workflows  
- confidential provider integrations, operational procedures, or security mechanisms  

Sandbox and production are **behaviorally equivalent** (same code and controls), but **physically isolated and do not share data**.

---

## Access & Engagement

Full documentation packs, diagrams, walkthroughs, and sandbox access are provided exclusively to:

- Issuer banks and safeguarding institutions  
- Electronic Money Institutions (EMIs)  
- BIN sponsors and card issuers  
- Regulated fintech platforms  
- Supervisory and audit bodies  

Requests for access:

- General & institutional: **contact@moncore.eu**  
- Partnerships & issuer onboarding: **partnerships@moncore.eu**  
- Compliance & regulatory: **compliance@moncore.eu**  

Please include: **institution, role, jurisdiction, and evaluation scope**.

---

**MonCore — Financial Operating System**  
Regulator-grade. Ledger-authoritative. Issuer-led by design.
