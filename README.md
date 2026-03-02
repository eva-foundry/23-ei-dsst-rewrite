# Project 23: EI-DSST Rewrite (ADMS-EI-SST Modernization)

**Project Start**: January 30, 2026  
**Status**: Discovery Phase  
**System**: Appeals Delivery and Management System - Employment Insurance - Social Security Tribunal (ADMS-EI-SST)

## Overview

Analysis and modernization planning for the ADMS-EI-SST legacy system - a critical government application managing Employment Insurance appeals and Social Security Tribunal cases.

## System Background

**ADMS-EI-SST** is a hybrid Oracle Forms client/server application with web components used by Service Canada to manage:
- Employment Insurance (EI) reconsideration requests
- Social Security Tribunal (SST) General Division cases
- Social Security Tribunal (SST) Appeal Division cases
- User access management and workload distribution

**Version**: 6.0  
**Technology Era**: 2013-2016  
**Current State**: Production (Legacy)

## Key Findings

### Technology Stack
- **Core Application**: Oracle Forms 12c on RHEL 7 (client/server)
- **Databases**: Oracle 19c on IBM AIX
- **Web Components**: ASP.NET on Windows Server 2016
- **Reporting**: IBM Cognos 11
- **Integration**: Point-to-point data exchangers, database links

### Critical Challenges
1. **Legacy Platform**: Oracle Forms is end-of-life path (mainstream support ends 2027)
2. **Client/Server Architecture**: Desktop-only, no mobile/remote flexibility
3. **Platform Heterogeneity**: Linux, Unix, Windows - complex to maintain
4. **RHEL 7**: Already end-of-life (June 2024)
5. **Integration Complexity**: Tightly coupled to Atrium, CCIS, CAAPS systems

## Project Artifacts

| Document | Description |
|----------|-------------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | Complete system architecture analysis |
| [SA-CONTEXT.md](./SA-CONTEXT.md) | **Solution Architect perspective** (Phase 0, repo risk, open questions) |
| [EPICS-AND-STORIES.md](./EPICS-AND-STORIES.md) | User stories extracted from documentation |
| [MEETING-PREP-FEB03.md](./MEETING-PREP-FEB03.md) | **Stakeholder meeting prep** (Feb 3, 2026, 1:05 PM) |
| [MODERNIZATION-ROADMAP.md](./MODERNIZATION-ROADMAP.md) | 6-month plan with GitHub Copilot |
| [TECHNICAL-ANALYSIS.md](./TECHNICAL-ANALYSIS.md) | Detailed technical assessment |
| [ONLINE-HELP-ANALYSIS.md](./ONLINE-HELP-ANALYSIS.md) | Documentation system analysis |

## Discovery Sources

1. **Online Help System**: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/
   - User profile management documentation
   - Access administration procedures
   - System screenshots and workflows

2. **Architecture Diagram**: Complete infrastructure diagram showing:
   - Application servers and databases
   - Integration points
   - Network architecture
   - Technology stack details

## Scope Definition

### In Scope (Phase 1 - 6 months)
- Employee Maintenance module modernization
- Proof of concept with modern tech stack
- API development and React UI
- Azure Gov Cloud deployment
- GitHub Copilot assisted development

### Out of Scope (Future Phases)
- Reconsideration module
- SST-GD and SST-AD modules
- Reporting system replacement
- Complete Oracle Forms decommission
- Database migration to cloud

## Success Criteria

- ✅ One complete module (Employee Maintenance) production-ready
- ✅ Modern architecture validated
- ✅ 33% development time savings with GitHub Copilot
- ✅ User acceptance testing with 10-15 Team Leaders
- ✅ CI/CD pipeline operational
- ✅ Documentation complete

## Team Structure

**Recommended**: 8-10 people
- 2 Solution Architects
- 4 Full-stack Developers
- 2 QA Engineers
- 1 DevOps Engineer
- 1 Business Analyst

## Timeline

- **Month 1-2**: Discovery & Foundation
- **Month 3-4**: Proof of Concept Development
- **Month 5-6**: Production Readiness & Deployment

## Stakeholders

- **Primary Users**: Team Leaders (APC), ADMS Managers, Service Canada Benefits Officers
- **Business Owner**: Appeals Processing Centre Directors
- **Technical Owner**: ESDC IT Architecture
- **Governance**: AICOE (AI Centre of Excellence)

## Related Systems

- **Atrium**: HR/Case management integration
- **CCIS**: Canadian Case Information System
- **CAAPS**: Canadian Appeals Processing System
- **Enterprise Directory**: Active Directory (APPS-ADMS-EI distribution list)

## Risk Register

| Risk | Level | Mitigation |
|------|-------|-----------|
| Oracle Forms complexity | HIGH | Thorough code analysis, expert consultation |
| User adoption resistance | MEDIUM | Early UAT involvement, training program |
| Government procurement delays | HIGH | Use existing Azure contracts |
| Security vulnerabilities (AI code) | MEDIUM | Mandatory code review + SAST tools |
| Scope creep | HIGH | Fixed scope: 1 module only |

## Next Steps

1. Schedule stakeholder kickoff meeting
2. Request access to Oracle Forms source code
3. Set up development environment
4. Begin code analysis and business logic extraction
5. Engage with current system users for requirements validation

## Contact

**Project Lead**: Marco Presta (marco.presta@hrsdc-rhdcc.gc.ca)  
**Organization**: AICOE (AI Centre of Excellence), ESDC

---

*Last Updated: January 30, 2026*
