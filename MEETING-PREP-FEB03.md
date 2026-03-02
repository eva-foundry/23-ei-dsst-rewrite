# Meeting Prep: ADMS-EI-SST Modernization & GitHub Copilot PoC

**Meeting**: AI model capabilities - ADMS-EI-SST Rewrite  
**Date**: February 3, 2026, 1:05 PM–2:00 PM (ET)  
**Prepared By**: Marco Presta, AICOE  
**Last Updated**: February 2, 2026

---

## 30-Second Pitch

ADMS-EI-SST is a **hybrid Oracle Forms 12c system** (not pure web) with critical EOL risks. We propose a **6-month GitHub Copilot PoC** targeting **Employee Maintenance module** to demonstrate 33% faster development, functional parity, and foundation for multi-year modernization.

**Critical Success Factor**: Complete **Phase 0 "Repo Truth Sprint"** (1-2 weeks) to establish source code baseline BEFORE development begins.

---

## Meeting Agenda (55 minutes)

1. **System Understanding** (10 min) - Validate our architecture analysis
2. **Critical Open Questions** (15 min) - 5 questions requiring stakeholder input
3. **PoC Scope & Timeline** (20 min) - Employee Maintenance module, 6-month roadmap
4. **Next Steps** (10 min) - Phase 0 initiation, access provisioning

---

- [Meeting Prep: ADMS-EI-SST Modernization \& GitHub Copilot PoC](#meeting-prep-adms-ei-sst-modernization--github-copilot-poc)
  - [30-Second Pitch](#30-second-pitch)
  - [Meeting Agenda (55 minutes)](#meeting-agenda-55-minutes)
  - [SECTION 1: System Understanding (To Validate)](#section-1-system-understanding-to-validate)
    - [Architecture Summary](#architecture-summary)
    - [End-of-Life Risks](#end-of-life-risks)
  - [SECTION 2: Critical Open Questions ⚠️ MUST RESOLVE](#section-2-critical-open-questions-️-must-resolve)
    - [Q1: Repository Baseline (HIGHEST PRIORITY)](#q1-repository-baseline-highest-priority)
    - [Q2: Business Logic Distribution](#q2-business-logic-distribution)
    - [Q3: Testing \& Parity](#q3-testing--parity)
    - [Q4: Integration Contracts](#q4-integration-contracts)
    - [Q5: Access Provisioning Timeline](#q5-access-provisioning-timeline)
  - [SECTION 3: PoC Scope \& Roadmap](#section-3-poc-scope--roadmap)
    - [Why Employee Maintenance Module?](#why-employee-maintenance-module)
    - [6-Month Roadmap](#6-month-roadmap)
      - [**Phase 0: Repo Truth Sprint (Weeks 1-2)** ⚠️ PREREQUISITE](#phase-0-repo-truth-sprint-weeks-1-2-️-prerequisite)
      - [**Month 1-2: Discovery \& Foundation**](#month-1-2-discovery--foundation)
      - [**Month 3-4: PoC Build**](#month-3-4-poc-build)
      - [**Month 5-6: Hardening \& UAT**](#month-5-6-hardening--uat)
    - [Explicit Non-Goals (6 Months)](#explicit-non-goals-6-months)
  - [SECTION 4: GitHub Copilot Value](#section-4-github-copilot-value)
    - [What Copilot IS \& IS NOT](#what-copilot-is--is-not)
    - [Expected Productivity Gains](#expected-productivity-gains)
    - [Governance \& Safety](#governance--safety)
  - [SECTION 5: Risks \& Mitigations](#section-5-risks--mitigations)
  - [SECTION 6: Success Criteria (Measurable)](#section-6-success-criteria-measurable)
    - [Functional Success](#functional-success)
    - [Quality Success](#quality-success)
    - [UAT Success](#uat-success)
    - [Productivity Success](#productivity-success)
  - [SECTION 7: Next Steps](#section-7-next-steps)
    - [From Stakeholders (NEEDED):](#from-stakeholders-needed)
    - [From AICOE (COMMITTED):](#from-aicoe-committed)
    - [Follow-Up Meetings](#follow-up-meetings)
  - [SECTION 8: Discussion Questions](#section-8-discussion-questions)
    - [Architecture (3 questions)](#architecture-3-questions)
    - [Repository (3 questions)](#repository-3-questions)
    - [Business Logic (3 questions)](#business-logic-3-questions)
    - [Integration (3 questions)](#integration-3-questions)
    - [Team (3 questions)](#team-3-questions)
    - [PoC Scope (3 questions)](#poc-scope-3-questions)
  - [Appendix: Reference Documents](#appendix-reference-documents)
    - [Project Documentation](#project-documentation)
    - [External Resources](#external-resources)
    - [Team Structure (Proposed)](#team-structure-proposed)
  - [Glossary](#glossary)
  
---

## SECTION 1: System Understanding (To Validate)

### Architecture Summary

**Our Understanding** (from [SA-CONTEXT.pdf](SA-CONTEXT.pdf) + [ARCHITECTURE.pdf](ARCHITECTURE.pdf)):

```
Users → Browser (AD_Login) → Authentication → Launch Oracle Forms Runtime (JRE)
                                               └─ Forms 12c on RHEL 7
                                                  └─ Oracle 19c DB (ADMSSST) on AIX
Supporting:
├─ ASP.NET Web Apps (login/help/lookup) on Windows 2016
├─ Atrium Data Exchanger (Windows Service) → SQL Server 2016
├─ CCIS integration (Oracle 19c)
└─ Cognos 11 reporting (ADMSR database)
```

**Questions**:
- ✅ Is this architecture accurate?
- ✅ Are versions correct (RHEL 7, Oracle 19c, Windows 2016)?
- ✅ Forms client = JRE-based or thick client install?

### End-of-Life Risks

| Technology | Version | EOL Status | Impact |
|------------|---------|------------|--------|
| **RHEL 7** | 7.x | ⚠️ **EOL June 2024** | Forms server unsupported |
| **Oracle Forms** | 12c | ⚠️ **Mainstream ends 2027** | Critical driver |
| **Windows 2016** | 2016 | ⚠️ **Mainstream ended 2022** | Web apps aging |
| **IBM AIX** | ? | ⚠️ **Legacy platform** | Skills declining |

**Question**: Organization's risk posture on these EOL timelines?

---

## SECTION 2: Critical Open Questions ⚠️ MUST RESOLVE

### Q1: Repository Baseline (HIGHEST PRIORITY)

**Current Status** (from [SA-CONTEXT.pdf](SA-CONTEXT.pdf)):
> "Oracle Forms code exists in **DEV - Repos**"  
> "**Caveat**: It is not clear which files are the latest version"

**Impact**: Without authoritative baseline:
- ❌ PoC sizing unreliable
- ❌ Business logic extraction blocked
- ❌ CI/CD impossible

**Questions**:
1. Where is authoritative Oracle Forms source?
2. Current branching strategy (main/develop/release)?
3. Are release tags present? Current production version?
4. Who are code owners for Employee Maintenance module?
5. Build/deployment process?

**Proposed Resolution**: **Phase 0 "Repo Truth Sprint" (1-2 weeks)**

---

### Q2: Business Logic Distribution

**Hypothesis** (from [SA-CONTEXT.pdf](SA-CONTEXT.pdf)):
- Forms triggers (WHEN-VALIDATE-ITEM, KEY-COMMIT, etc.)
- PL/SQL packages/procedures
- DB triggers, constraints, defaults
- Implicit transaction/locking semantics

**Questions**:
1. For **Employee Maintenance** specifically, where does logic reside?
2. Are business rules documented separately?
3. Logic distribution estimate: Forms vs. PL/SQL vs. DB? (%)
4. Integration points with other modules?

**Resolution**: Logic inventory workshop (2-3 days in Month 1)

---

### Q3: Testing & Parity

**Gap** (from [SA-CONTEXT.pdf](SA-CONTEXT.pdf)):
> "Oracle Forms behavior is the 'spec.' Without regression harness: parity cannot be demonstrated"

**Questions**:
1. Test suite exists for Employee Maintenance?
2. How is regression testing done today?
3. Acceptance criteria for "functional parity"?
4. Who validates PoC parity? (UAT team: 10-15 Team Leaders)

**Resolution**: Capture Forms behavior as baseline (screen recordings, validation rules)

---

### Q4: Integration Contracts

**Known Integrations** (from [ARCHITECTURE.pdf](ARCHITECTURE.pdf)):
- Atrium (HR sync via Data Exchanger)
- CCIS (case info, likely DB links)
- Active Directory (APPS-ADMS-EI)
- Cognos (IRIS reporting)

**Questions**:
1. Which integrations critical for Employee Maintenance PoC?
2. Integration contracts documented?
3. Test against integration systems in dev/UAT?
4. Rollback process if integration breaks?

**Resolution**: Map integration touch points, document contracts (pre-PoC)

---

### Q5: Access Provisioning Timeline

**Requirements**:
- Repository access (DEV - Repos)
- Database profiles (ADMSSST, ADMSR)
- JRE installation (via NSD)
- Active Directory (APPS-ADMS-EI)
- UAT environment (`https://intradev16-u.intra.dev/AS-SA/AD_Login/`)

**Questions**:
1. Access provisioning turnaround time? (NSD ticket)
2. PoC team can access production (read-only)?
3. Security clearance requirements?
4. Microsoft DevBox or standard workstations?

**Resolution**: Submit access requests Week 1 of Phase 0

---

## SECTION 3: PoC Scope & Roadmap

### Why Employee Maintenance Module?

**Rationale** (from [EPICS-AND-STORIES.pdf](EPICS-AND-STORIES.pdf)):
1. ✅ **Well Documented**: 36 user stories from online help v6.0
2. ✅ **Clear CRUD**: Employee profile management
3. ✅ **Exercises RBAC**: Profile types, hierarchical permissions
4. ✅ **Lower Risk**: Admin vs. case management
5. ✅ **Testable**: 10-15 Team Leaders for UAT

**Coverage**: 10 epics, 36 user stories, ~123 story points

### 6-Month Roadmap

#### **Phase 0: Repo Truth Sprint (Weeks 1-2)** ⚠️ PREREQUISITE

**Deliverables**:
- ✅ Authoritative source confirmed
- ✅ Build/run instructions
- ✅ Code owners identified
- ✅ Architecture validated
- ✅ Access provisioning complete

**Exit Criteria**: Can build Employee Maintenance from source

---

#### **Month 1-2: Discovery & Foundation**

**Deliverables**:
1. Business Logic Inventory (Forms/PL-SQL/DB mapping)
2. Parity Test Baseline (expected behavior matrix)
3. Modern Architecture Design (.NET Core 8 + React)
4. Dev Environment Setup (CI/CD pipeline)

---

#### **Month 3-4: PoC Build**

**Deliverables**:
1. Backend API (.NET Core 8 with Entity Framework)
2. Frontend UI (React + TypeScript, bilingual)
3. Database Layer (Repository pattern)
4. Integration Testing (xUnit, Playwright)

---

#### **Month 5-6: Hardening & UAT**

**Deliverables**:
1. Security Hardening (OWASP, pen testing)
2. Performance Testing (load, stress)
3. UAT with 10-15 Team Leaders
4. Production Readiness

---

### Explicit Non-Goals (6 Months)

❌ **NOT in scope**:
- Reconsideration module
- SST-GD/SST-AD modules
- Reporting platform replacement
- Database migration off Oracle
- Decommissioning Forms

✅ **IN scope**:
- Employee Maintenance only
- Foundation architecture (reusable)
- Modernization feasibility proof
- GitHub Copilot productivity gains

---

## SECTION 4: GitHub Copilot Value

### What Copilot IS & IS NOT

**Copilot IS**:
- ✅ Productivity amplifier (boilerplate, tests, docs)
- ✅ Pattern recognition for standard code

**Copilot IS NOT**:
- ❌ Architecture replacement (we still analyze)
- ❌ Security guarantee (human review required)
- ❌ Forms logic extractor (we do that manually)

### Expected Productivity Gains

**Conservative Estimates**:
- Code generation: 30-40% faster
- Documentation: 50% faster
- Test writing: 40% faster
- **Overall: 33% faster** (123 SP in 8 weeks vs 12 traditional)

### Governance & Safety

**Mandatory Controls**:
1. Human review of all AI suggestions
2. Automated security scanning (SAST)
3. No sensitive data in prompts
4. Developers own code, not AI
5. All code must pass tests

---

## SECTION 5: Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| **Hidden Forms logic** | HIGH | HIGH | Phase 0 inventory + parity tests |
| **Repo ambiguity** | HIGH | HIGH | Repo Truth Sprint (Weeks 1-2) |
| **Security vulns** | MED | HIGH | Review + SAST + pen test |
| **Integration breaks** | MED | MED | Contract mapping + test env |
| **Scope creep** | HIGH | MED | Strict definition + change control |
| **Access delays** | MED | LOW | Pre-provision in Phase 0 |

---

## SECTION 6: Success Criteria (Measurable)

### Functional Success
- ✅ 36 user stories with parity
- ✅ Validation rules preserved
- ✅ Error codes maintained (ADMS 828)
- ✅ Bilingual (English/French)

### Quality Success
- ✅ 80% code coverage
- ✅ Zero high-severity vulns
- ✅ WCAG 2.1 Level AA
- ✅ <2 second load time (95th percentile)

### UAT Success
- ✅ 80%+ user satisfaction
- ✅ 10-15 Team Leaders complete UAT
- ✅ Business sign-off

### Productivity Success
- ✅ 33% faster development
- ✅ Measurable Copilot contribution
- ✅ Developer satisfaction

---

## SECTION 7: Next Steps

### From Stakeholders (NEEDED):
1. ✅ Confirm repo location & access
2. ✅ Provide architecture diagram (if exists)
3. ✅ Identify Employee Maintenance code owners
4. ✅ Confirm UAT team (10-15 Team Leaders)
5. ✅ Approve Phase 0 (Weeks 1-2)

### From AICOE (COMMITTED):
1. ✅ Initiate access provisioning
2. ✅ Schedule Phase 0 kickoff
3. ✅ Assemble PoC team (8-10 people)
4. ✅ Prepare Phase 0 work plan

### Follow-Up Meetings

- **Week 2 (Feb 10)**: Phase 0 progress (30 min)
- **Week 4 (Feb 24)**: Phase 0 completion (1 hour)
- **Week 8 (Mar 23)**: Month 1-2 demo (1 hour)
- **Week 16 (May 18)**: Month 3-4 PoC demo (1 hour)
- **Week 24 (Jul 13)**: UAT readiness (1 hour)
- **Week 26 (Jul 27)**: Final PoC presentation (2 hours)

---

## SECTION 8: Discussion Questions

### Architecture (3 questions)
1. Technology stack accurate? (RHEL 7, Oracle 19c, AIX)
2. Undocumented systems/integrations?
3. Current DR/HA strategy?

### Repository (3 questions)
4. Authoritative Forms source location?
5. Release/branching strategy?
6. Access production code (read-only)?

### Business Logic (3 questions)
7. Employee Maintenance logic: Forms/PL-SQL/DB split?
8. Test suite exists?
9. Regression testing process?

### Integration (3 questions)
10. Critical integrations for PoC?
11. Integration contracts documented?
12. Test in dev/UAT environments?

### Team (3 questions)
13. Employee Maintenance SMEs?
14. Access provisioning timeline?
15. Security clearance requirements?

### PoC Scope (3 questions)
16. 6-month timeline aligns with priorities?
17. Employee Maintenance = right choice?
18. Success expectations at end of PoC?

---

## Appendix: Reference Documents

### Project Documentation
- **[README.md](README.md)**: Project overview
- **[ARCHITECTURE.pdf](ARCHITECTURE.pdf)**: Technical analysis (600 lines)
- **[SA-CONTEXT.pdf](SA-CONTEXT.pdf)**: Solution Architect perspective
- **[EPICS-AND-STORIES.pdf](EPICS-AND-STORIES.pdf)**: 36 user stories
- **[.github/copilot-instructions.md](.github/copilot-instructions.md)**: Dev patterns

### External Resources
- **Online Help**: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/ (v6.0)
- **UAT Environment**: https://intradev16-u.intra.dev/AS-SA/AD_Login/

### Team Structure (Proposed)
- 2 Solution Architects
- 4 Full-stack Developers
- 2 QA Engineers
- 1 DevOps Engineer
- 1 Business Analyst

---

## Glossary

- **ADMS-EI-SST**: Appeals Decision Management System
- **APC**: Appeals Processing Centre
- **BEA**: Business Expertise Advisor
- **SCBO**: Service Canada Benefits Officer
- **SST-GD/AD**: Social Security Tribunal General/Appeal Division
- **NHQ**: National Headquarters
- **UAT**: User Acceptance Testing
- **NSD**: National Service Desk

---

**Document Status**: Ready for Feb 3, 2026 meeting  
**Next Review**: Post-meeting (capture decisions)
