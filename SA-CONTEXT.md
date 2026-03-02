# ADMS-EI-SST — Modernization & GitHub Copilot PoC SA-CONTEXT.MD
2026-02-02 06:10

**Context (Jan–Feb 2026):** This README captures what has been discovered so far about the **ADMS-EI-SST** system (Appeals Decision Management System – Employment Insurance / Social Security Tribunal) and proposes a **realistic, evidence-driven PoC** using **GitHub Copilot** inside the ESDC development environment.

> **Important:** Several items below are still **hypotheses** pending confirmation with system owners (notably anything about “direct DB connections” and “which repo files are current”). Treat this doc as a working baseline for the Feb 3, 2026 meeting.
## 📚 Related Documentation

- **[README.md](README.md)** - Project overview
- **[ARCHITECTURE.pdf](ARCHITECTURE.pdf)** - Technical deep-dive (600 lines)
- **[EPICS-AND-STORIES.pdf](EPICS-AND-STORIES.pdf)** - 36 user stories
- **[MEETING-PREP-FEB03.pdf](MEETING-PREP-FEB03.pdf)** - Meeting prep (Feb 3, 2026)

**Perspective Alignment**: This SA document focuses on operational/risk concerns. [ARCHITECTURE.pdf](ARCHITECTURE.pdf) provides complementary technical analysis. Both are 100% consistent on system understanding.
---

## 1. Executive Summary

ADMS-EI-SST is **not a pure web application**. It is a **hybrid** system where users **launch Oracle Forms through an intranet web entry point**, and the **core case work happens in Oracle Forms** (Forms runtime requires **JRE** on user workstations). There are also supporting **ASP.NET** web apps (login/lookup/help/related apps), multiple databases (Oracle + SQL Server), an integration service, and Cognos reporting.

A full replacement is **multi-year**. A 6-month effort can realistically deliver **Foundation + one module PoC** (a vertical slice), while proving that modernization is feasible under ESDC constraints.

---

## 2. What We Know (So Far)

### 2.1 Entry point / UAT environment (confirmed by stakeholders)
- Oracle Forms application is launched through UAT:
  - `https://intradev16-u.intra.dev/AS-SA/AD_Login/`
- Workstation prerequisites:
  - **Java Runtime Environment (JRE)** installed via NSD
  - **Active Directory membership**
  - **Database profile** provisioned

### 2.2 Code repository (confirmed, with a major caveat)
- Oracle Forms code repository exists in **DEV - Repos**
- **Caveat:** “It is not clear which files are the latest version”  
  → baseline/tagging/release truth must be established early.

---

## 3. Current Architecture (Hybrid)

### 3.1 High-level picture (validated directionally; refine with owners)

- **User** begins in **browser** (launcher/auth/help)
- **Oracle Forms runtime (JRE-based)** is required on the workstation
- Core application executes as **Oracle Forms 12c**
- Data persists primarily in **Oracle 19c** (ADMSSST DB)
- Supporting systems include SQL Server (Atrium), other Oracle DBs (CCIS), reporting DB and Cognos, and middleware sync services

### 3.2 Simplified architecture diagram (working model)

```

┌──────────────────────────────────────────────────────────────┐
│                          User Layer                           │
│  ┌──────────────────────┐        ┌─────────────────────────┐ │
│  │ Web Browser           │        │ Oracle Forms Runtime     │ │
│  │ (AD_Login / Help)     │        │ (JRE on workstation)     │ │
│  └─────────┬────────────┘        └────────────┬────────────┘ │
└────────────┼───────────────────────────────────┼──────────────┘
│                                   │
┌────────────▼───────────────┐     ┌─────────────▼─────────────┐
│ Web/App Tier (ESDC network) │     │ Oracle Forms App Tier      │
│ - ASP.NET login/help/lookup │     │ - Oracle Forms 12c server  │
│ - AD integration services   │     │ - RHEL platform (legacy)   │
└────────────┬───────────────┘     └─────────────┬─────────────┘
│                                   │
┌────────────▼───────────────────────────────────▼──────────────┐
│                           Data Tier                             │
│  ┌────────────────────┐  ┌────────────────────┐  ┌──────────┐ │
│  │ ADMSSST (Oracle 19c)│  │ Atrium (SQL Server) │  │ CCIS     │ │
│  │ Main operational DB │  │ Integration DB      │  │ Oracle   │ │
│  └────────────────────┘  └────────────────────┘  └──────────┘ │
│       │                                                         │
│       ▼                                                         │
│  ┌────────────────────┐                                         │
│  │ ADMSR Reporting DB  │  →  IBM Cognos 11 (IRIS reports)        │
│  └────────────────────┘                                         │
└─────────────────────────────────────────────────────────────────┘

```

---

## 4. Technology Stack Inventory (Working)

### 4.1 Core application layer
- **Oracle Forms 12c**
- Deployed in a way that requires **JRE** on the workstation
- Forms server platform referenced as **RHEL 7** (to confirm)

### 4.2 Databases
- **ADMSSST** — Oracle 19c (platform referenced as IBM AIX)
- **ADMSR** — reporting database (Oracle 19x) (platform referenced as IBM AIX)
- **Atrium** — Microsoft SQL Server 2016 (Windows 2016)
- **CCIS** — Oracle 19c (platform referenced as IBM “MX”)

### 4.3 Web application layer (supporting)
ASP.NET applications (Windows 2016 referenced):
- ADMS Lookup Web Service (data lookup APIs)
- ADMS Login Web Application (launcher/auth portal)
- ADMS-EI-SST Help Web Application (online help)
- CAAPS Web Application (related system)

### 4.4 Integration middleware
- Atrium/ADMIS – Data Exchanger (Windows service) for bidirectional synchronization

### 4.5 Reporting layer
- IBM Cognos 11 (IRIS – ADMS-EI-SST Reports), using ADMSR reporting DB

### 4.6 Directory services / identity
- Active Directory via an Enterprise Directory Web Service
- Distribution list referenced: APPS-ADMS-EI (confirm exact usage)

---

## 5. User Workflow (Observed / Hypothesized)

### 5.1 End-user flow
1. User opens **AD_Login** in browser (UAT URL above)
2. User authenticates (via AD / directory service)
3. User launches **Oracle Forms runtime session** (JRE-based client)
4. User performs operational work in Oracle Forms
5. User may consult browser-based online help

### 5.2 Data flow patterns (working model)
Operational:
```

Forms runtime → Forms server → ADMSSST Oracle DB

```

Integration:
```

ADMSSST ↔ Data Exchanger service ↔ Atrium SQL Server
ADMSSST ↔ CCIS (via DB links/views)   [to confirm]

```

Reporting:
```

ADMSSST → (ETL/batch) → ADMSR reporting DB → Cognos reports

```

---

## 6. Functional Scope (From Online Help Discovery)

The online help (version referenced as **6.0**) reveals:
- Modules:
  - Reconsideration
  - SST-GD (General Division)
  - SST-AD (Appeal Division)
  - System Administration → Employee Maintenance
- Multi-office support:
  - Atlantic-APC, Ontario-APC, Quebec-APC, Western-APC, NHQ
- Business rules surfaced via help text / errors (examples):
  - Single active profile enforcement
  - Auto-expiry behavior when changing profiles
  - Permission hierarchy (can only modify lower levels)
  - “New employee” restricted permissions workflow (SCBO training / BEA review)
- UI capabilities implied:
  - Search, CRUD forms, dropdowns/picklists
  - Print/export (PDF/Spreadsheet)
  - Notifications (email)
  - Role-based access control

> **Note:** The help site exhibits classic ASP.NET WebForms characteristics (.aspx, postbacks, TreeView). This likely represents the help/launcher surface, not the core Forms UI.

---

## 7. Primary “Gotchas” for a Rewrite (Why this is not a simple UI rebuild)

### 7.1 Oracle Forms hides business logic everywhere
Expect logic in:
- Forms triggers and validations (WHEN-VALIDATE-ITEM, KEY-COMMIT, PRE-INSERT, etc.)
- PL/SQL packages/procedures
- DB triggers/constraints/defaults
- Implicit transaction/locking semantics

### 7.2 Environment reproducibility constraints
- Workstation dependency on JRE and possibly client configs
- NSD-managed installs
- AD membership and DB profiles required
- Hard-to-reproduce “works on my machine” behavior unless standardized dev environments exist

### 7.3 Integration/reporting complexity
- Atrium sync service can create hidden coupling
- CCIS and CAAPS dependencies may be embedded in DB links or shared entities
- Cognos/reporting DB separation adds migration requirements beyond “app rewrite”

### 7.4 Repo baseline ambiguity
If “latest version” is unclear:
- migration sizing is unreliable
- Copilot PoC credibility suffers
- CI/CD and release traceability must be established first

### 7.5 Testing gap is often the real blocker
Oracle Forms behavior is the “spec.” Without a regression harness:
- parity cannot be demonstrated
- modernization risk remains high even if code is produced quickly

---

## 8. GitHub Copilot PoC — Scope & Approach

### 8.1 What Copilot is (and is not)
Copilot is:
- a productivity amplifier for boilerplate, scaffolding, tests, docs, and standard patterns

Copilot is not:
- a substitute for architecture and systems analysis
- a guarantee of secure code
- a replacement for regression truth or data governance

### 8.2 PoC goal (recommended)
Demonstrate we can modernize a **single vertical slice** with:
- validated functional parity for that slice
- secure coding practices and required governance
- reproducible build/test pipeline
- measurable productivity gains

### 8.3 PoC candidate module
A lower-risk PoC often targets **System Administration → Employee Maintenance** because:
- it is well documented (online help)
- tends to have clearer CRUD boundaries
- can still exercise RBAC rules and workflows (profiles, expiry, permissions)

### 8.4 PoC prerequisites (Phase 0 “Repo Truth + Architecture Validation”)
Before writing new code, complete:
1. Identify authoritative repo path(s), branch strategy, release tags
2. Establish “latest” artifacts and build steps
3. Confirm deployment model (Forms services vs thick client assumptions)
4. Inventory where business rules live for chosen slice
5. Capture parity tests (expected behavior)

---

## 9. Proposed 6-Month Roadmap (Realistic)

### Month 1–2: Discovery & Foundation
Deliverables:
- confirmed architecture diagram + data flow
- “repo truth” baseline + build/run instructions
- business rule inventory for chosen slice (triggers/PLSQL/DB)
- initial automated regression harness for the slice

### Month 3–4: PoC Build (One module)
Deliverables:
- modern UI + API for chosen slice
- stable integration strategy (likely keep Oracle DB in PoC)
- RBAC behavior parity for the slice
- basic observability (logs/audit events)

### Month 5–6: Hardening, UAT, Deployment-readiness
Deliverables:
- security hardening (OWASP basics, audit logging)
- CI/CD to dev/stage
- UAT with a small group of users (10–15)
- PoC outcomes: parity, risks, sizing for multi-year program

> **Explicit non-goals (6 months):**
- full replacement of reconsideration/SST modules
- reporting platform replacement
- database migration off Oracle
- decommissioning Oracle Forms

---

## 10. Risks & Mitigations

| Risk | What it looks like | Mitigation |
|---|---|---|
| Hidden Forms/DB logic | “We matched UI but behavior differs” | logic inventory + automated parity tests |
| Repo ambiguity | “Which source is current?” | Phase 0 repo truth + tagging |
| Security vulnerabilities | insecure AI suggestions | mandatory human review + static analysis |
| Integration breaks | Atrium/CCIS side effects | integration contract mapping + test env |
| Scope creep | “Let’s just do another module…” | strict PoC slice definition + change control |
| Access constraints | AD/DB profiles/JRE provisioning delays | pre-provision PoC team accounts early |

---

## 11. Open Questions (Must Confirm)

1. Is the Forms client delivered via **Oracle Forms Services (webstart/JRE)**, or a thick client install, or both?
2. What is the authoritative **system diagram/data flow** (if one exists) and where is it stored?
3. Which repo folders are current, and what is the release process?
4. Where do the core business rules reside for the chosen module (Forms triggers vs PL/SQL vs DB)?
5. How are audit logs and security controls implemented today (and what must be preserved)?

---

## 12. Meeting Notes Reference (Feb 3, 2026)

**Meeting:** AI model capabilities - ADMS-EI-SST Rewrite  
**When:** Feb 3, 2026 1:05 PM–2:00 PM (ET)  
**Agenda:** rewrite project, Copilot pilot, next steps

---

## 13. Next Actions (Recommended)

### A) Phase 0 — Repo Truth Sprint (1–2 weeks)
- confirm “latest” source
- establish build/run steps
- identify owners per subsystem
- produce validated architecture diagram and data flows

### B) Pick PoC slice and write parity tests
- choose one module + one edge-case workflow
- capture expected behavior (validations, errors, audit)
- create regression suite baseline

### C) Stand up modern scaffolding under ESDC governance
- standard templates, linting, unit tests
- CI pipeline and security scanning
- Copilot usage guidance (prompt hygiene; no sensitive data)

---

## 14. Glossary

- **ADMS-EI-SST**: Appeals Decision Management System – Employment Insurance / Social Security Tribunal
- **SST-GD / SST-AD**: Social Security Tribunal – General Division / Appeal Division
- **APC**: Appeals Processing Centre (regional)
- **SCBO / BEA**: role types referenced in workflows (confirm exact expansions)
- **Forms**: Oracle Forms 12c
- **ADMSSST**: primary operational Oracle DB
- **ADMSR**: reporting DB
- **Atrium**: integrated system/database (SQL Server)
- **CCIS / CAAPS**: related integrated systems (confirm interfaces)
- **UAT**: User Acceptance Testing environment
- **NSD**: National Service Desk

---

## 15. Disclaimer

This README consolidates early discovery notes and stakeholder emails. Treat all platform/version statements as **working assumptions** until confirmed by system owners and authoritative documentation.
```
