# ADMS-EI-SST System Architecture

**Document Version**: 1.0  
**Date**: January 30, 2026  
**Source**: Architecture diagram analysis + online help documentation

---

## Executive Summary

ADMS-EI-SST is a **hybrid Oracle Forms client/server application** with supporting web components, not a pure web application as initially assumed. The system manages Employment Insurance appeals and Social Security Tribunal cases across Canada.

**Critical Discovery**: Users work primarily in Oracle Forms thick client, with web portal only for authentication and help documentation.

---

## System Components

### 1. Core Application Layer

#### **ADMS-EI-SST Client/Server Application**
- **Technology**: Oracle Forms 12c
- **Runtime Environment**: RHEL 7 (Red Hat Enterprise Linux 7)
- **Architecture**: Traditional 2-tier client/server
- **Client Distribution**: Oracle Forms Runtime must be installed on user workstations
- **Database Connectivity**: Direct connection to Oracle database
- **User Base**: Appeals Processing Centre staff across 4 regional offices + NHQ

**Lifecycle Status**: ⚠️ **Oracle Forms mainstream support ends 2027**

**Functionality**:
- Case management for EI reconsideration requests
- SST-GD (General Division) case workflow
- SST-AD (Appeal Division) case workflow
- Employee profile management
- Workload assignment and distribution
- Decision tracking and reporting

---

### 2. Database Tier

#### **Primary Database: ADMSSST**
- **Technology**: Oracle Database 19c
- **Platform**: IBM AIX (Unix)
- **Purpose**: Main operational database (OLTP)
- **Contents**:
  - Employee profiles and access control
  - Case/appeal records
  - Workflow state
  - Decision data
  - Audit logs
- **High Availability**: Likely RAC configuration (to be verified)

#### **Reporting Database: ADMSR**
- **Technology**: Oracle Database 19t
- **Platform**: IBM AIX
- **Purpose**: Dedicated reporting/analytics (OLAP)
- **Data Flow**: ETL from ADMSSST (likely batch nightly)
- **Pattern**: Separation of OLTP and OLAP workloads

#### **Integration Databases**

**Atrium Database**:
- **Technology**: Microsoft SQL Server 2016
- **Platform**: Windows Server 2016
- **Purpose**: Third-party HR/case management system
- **Integration**: Bidirectional data exchange via Data Exchanger service

**CCIS Database**:
- **Technology**: Oracle 19c
- **Platform**: IBM MX
- **Purpose**: Canadian Case Information System
- **Integration**: Database links for read access

---

### 3. Web Application Layer

All web applications run on **Windows Server 2016** with **ASP.NET** (likely .NET Framework 4.5-4.7).

#### **ADMS Login Web Application**
- **URL**: https://intradev16-u.intra.dev/AS-SA/...
- **Purpose**: Initial authentication portal
- **Flow**:
  1. User logs in via browser
  2. Active Directory authentication (APPS-ADMS-EI distribution list)
  3. User launches Oracle Forms client
  4. Forms client connects directly to ADMSSST database

#### **ADMS Lookup Web Service**
- **Type**: ASP.NET Web Service (SOAP/REST)
- **Purpose**: Provide lookup data to other systems
- **Consumers**: Forms application, other web apps
- **Data**: Pick lists, reference tables, validation rules

#### **ADMS-EI-SST Help Web Application**
- **URL**: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/
- **Technology**: ASP.NET Web Forms (classic postback model)
- **Purpose**: Context-sensitive help documentation
- **Version**: 6.0 (copyright 2013)
- **Features**:
  - TreeView navigation
  - Screenshot galleries
  - Printable help pages
  - English/French bilingual

#### **CAAPS Web Application**
- **Purpose**: Canadian Appeals Processing System integration
- **Integration**: Likely shares data with ADMSSST via database links

---

### 4. Integration Middleware

#### **Atrium/ADMIS Data Exchanger**
- **Type**: Windows Service
- **Platform**: Windows Server 2016
- **Purpose**: Real-time bidirectional data synchronization
- **Data Flow**:
  - ADMSSST ↔ Atrium Database
  - Keeps HR data (employee profiles) in sync
  - Handles case status updates
- **Pattern**: Point-to-point integration (tightly coupled)

---

### 5. Reporting Infrastructure

#### **IRIS - ADMS-EI-SST Reports**
- **Technology**: IBM Cognos 11
- **Data Source**: ADMSR Reporting Database
- **Purpose**: Business intelligence and operational reporting
- **Users**: Managers, analysts, executives
- **Report Types**:
  - Active employee lists by profile/office
  - Case workload distribution
  - Performance metrics
  - Compliance reports

---

### 6. Directory Services

#### **Enterprise Directory Web Service**
- **Technology**: WSED (Web Services for Enterprise Directory)
- **Backend**: Active Directory
- **Purpose**: Authentication and user management
- **Key Functionality**:
  - User authentication
  - Distribution list management (APPS-ADMS-EI)
  - Group membership queries
  - Profile validation

**Critical Distribution List**: **APPS-ADMS-EI**
- All ADMS users must be members
- Membership grants initial "Enquiries" level access
- Team Leaders/Managers elevate privileges via Employee Maintenance

---

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        User Workstations                             │
│  ┌──────────────────┐              ┌─────────────────────────────┐ │
│  │  Web Browser     │              │ Oracle Forms Runtime Client │ │
│  │  (Login + Help)  │              │ (Thick Client)              │ │
│  └────────┬─────────┘              └──────────┬──────────────────┘ │
└───────────┼────────────────────────────────────┼──────────────────┘
            │ HTTPS                               │ SQL*Net
            │                                     │
┌───────────▼─────────────────────────────────────▼──────────────────┐
│                    ESDC Network (Intranet)                          │
│  ┌──────────────────────────┐    ┌──────────────────────────────┐ │
│  │  Web Application Tier    │    │  Forms Application Tier      │ │
│  │  (Windows Server 2016)   │    │  (RHEL 7)                    │ │
│  │  ┌────────────────────┐  │    │  ┌────────────────────────┐ │ │
│  │  │ ADMS Login         │  │    │  │ Oracle Forms 12c       │ │ │
│  │  │ ADMS Lookup        │  │    │  │ Application Server     │ │ │
│  │  │ ADMS Help          │  │    │  └────────────────────────┘ │ │
│  │  │ CAAPS              │  │    │                              │ │
│  │  └────────────────────┘  │    │                              │ │
│  └──────────┬───────────────┘    └───────────┬──────────────────┘ │
│             │                                 │                     │
│  ┌──────────▼───────────────────────────────▼──────────────────┐  │
│  │              Integration Middleware                          │  │
│  │  ┌────────────────────────────────────────────────────────┐ │  │
│  │  │  Atrium/ADMIS Data Exchanger (Windows Service)         │ │  │
│  │  └────────────────────────────────────────────────────────┘ │  │
│  └──────────┬──────────────────────────────┬───────────────────┘  │
└─────────────┼──────────────────────────────┼──────────────────────┘
              │                              │
┌─────────────▼──────────────────────────────▼──────────────────────┐
│                         Database Tier                              │
│  ┌─────────────────┐  ┌──────────────┐  ┌───────────────────┐   │
│  │  ADMSSST DB     │  │  Atrium DB   │  │  CCIS DB          │   │
│  │  Oracle 19c     │  │  SQL Server  │  │  Oracle 19c       │   │
│  │  (IBM AIX)      │  │  2016        │  │  (IBM MX)         │   │
│  │  [OLTP]         │  │  (Windows)   │  │                   │   │
│  └───────┬─────────┘  └──────────────┘  └───────────────────┘   │
│          │ ETL                                                     │
│  ┌───────▼─────────┐                                              │
│  │  ADMSR DB       │                                              │
│  │  Oracle 19t     │                                              │
│  │  (IBM AIX)      │                                              │
│  │  [OLAP]         │                                              │
│  └───────┬─────────┘                                              │
└──────────┼──────────────────────────────────────────────────────────┘
           │
┌──────────▼──────────────────────────────────────────────────────────┐
│                     Reporting Layer                                  │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │  IRIS - ADMS-EI-SST Reports (IBM Cognos 11)                   │ │
│  └────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Network Architecture

### **ESDC Intranet**
All components deployed within Employment and Social Development Canada (ESDC) internal network.

**Network Zones**:
1. **User Zone**: Workstations with Forms runtime
2. **Application Zone**: Web servers (Windows) and Forms servers (RHEL)
3. **Database Zone**: Oracle and SQL Server instances
4. **Integration Zone**: Data exchanger services

**Security**:
- Firewalls between zones
- No direct internet access
- VPN required for remote access
- Active Directory integrated authentication

---

## Data Flow Patterns

### 1. User Authentication Flow

```
1. User → Browser → ADMS Login Web App
2. Login App → Enterprise Directory → Active Directory
3. AD validates credentials + APPS-ADMS-EI membership
4. Login App → User: Authentication success
5. User launches Oracle Forms Runtime Client
6. Forms Client → Forms Server (RHEL 7)
7. Forms Server → ADMSSST Database (direct SQL*Net)
8. User performs case work in Forms application
```

### 2. Employee Profile Management Flow

```
1. Team Leader logs into Forms application
2. Navigates to System Administration → Employee Maintenance
3. Forms application queries ADMSSST database
4. Team Leader updates employee profile
5. Forms commits transaction to ADMSSST
6. Data Exchanger (background service) detects change
7. Data Exchanger syncs to Atrium Database
8. Atrium HR system reflects updated employee info
```

### 3. Reporting Data Flow

```
1. [Nightly ETL Process]
2. ETL extracts data from ADMSSST (OLTP)
3. ETL transforms and loads into ADMSR (OLAP)
4. IBM Cognos queries ADMSR for reports
5. Users access reports via Cognos portal
6. No impact on operational ADMSSST performance
```

### 4. Case Data Integration Flow

```
1. User works on case in ADMS Forms
2. ADMS queries CCIS database (via DB link)
3. ADMS displays related case information
4. User makes decision in ADMS
5. Decision recorded in ADMSSST
6. Trigger updates CCIS via DB link (if applicable)
```

---

## Technology Stack Analysis

### Platform Diversity

| Platform | Purpose | EOL Status |
|----------|---------|------------|
| **RHEL 7** | Oracle Forms server | ⚠️ **EOL June 2024** |
| **IBM AIX** | Oracle databases | ⚠️ Legacy, limited skills |
| **Windows Server 2016** | Web apps, Data Exchanger | ⚠️ Mainstream support ended 2022 |

### Application Technologies

| Technology | Version | EOL Status |
|------------|---------|------------|
| **Oracle Forms** | 12c | ⚠️ Mainstream support ends 2027 |
| **Oracle Database** | 19c | ✅ Supported until 2027 |
| **ASP.NET** | Framework 4.x | ⚠️ No longer evolved (use Core) |
| **IBM Cognos** | 11 | ✅ Supported |

### Integration Patterns

| Pattern | Technology | Assessment |
|---------|------------|------------|
| **Database Links** | Oracle | ⚠️ Tight coupling, hard to scale |
| **Data Exchanger Service** | Custom Windows Service | ⚠️ Single point of failure |
| **Direct SQL*Net** | Oracle protocol | ⚠️ No abstraction layer |

---

## Architectural Challenges

### 1. **Oracle Forms Dependency**

**Problem**: Oracle Forms is legacy technology with declining support and developer availability.

**Impact**:
- Difficult to find skilled developers
- Limited modern UI/UX capabilities
- Desktop-only (no mobile, no responsive design)
- Thick client deployment complexity
- Tight coupling to Oracle database

**Modernization Path**: Replace with modern web framework (React, Angular, Vue)

---

### 2. **Client/Server Architecture Limitations**

**Problem**: Traditional 2-tier architecture with thick client.

**Limitations**:
- ❌ No mobile access
- ❌ No remote work flexibility (requires VPN + client install)
- ❌ Difficult to scale (client updates require desktop deployment)
- ❌ Hard to implement modern security patterns (API gateways, OAuth)
- ❌ Limited to Windows/Linux desktop clients

**Modernization Path**: 3-tier web architecture with API layer

---

### 3. **Platform Heterogeneity**

**Problem**: Mix of Linux, Unix, and Windows platforms.

**Operational Complexity**:
- Multiple OS patch schedules
- Different security policies per platform
- Varied skill sets required (AIX admins, Windows admins, Linux admins)
- Complex disaster recovery procedures
- Higher licensing costs

**Modernization Path**: Standardize on cloud-native (containers, Kubernetes)

---

### 4. **Tight Coupling via Database Links**

**Problem**: Systems integrated via direct database connections.

**Issues**:
- Schema changes impact multiple systems
- No versioning or contract enforcement
- Difficult to troubleshoot integration issues
- Security concerns (database credentials shared)
- Performance unpredictable (network latency)

**Modernization Path**: API-first architecture with service mesh

---

### 5. **Point-to-Point Integration**

**Problem**: Data Exchanger service is custom point-to-point integration.

**Risks**:
- Single point of failure
- No retry/dead letter queue
- Hard to add new systems
- Difficult to monitor data flow
- No audit trail of data changes

**Modernization Path**: Event-driven architecture (Azure Service Bus, Event Grid)

---

## Security Architecture

### Authentication & Authorization

**Current State**:
- Active Directory authentication (APPS-ADMS-EI distribution list)
- Role-based access control (RBAC) in application
- Profiles: Clerk, SCBO, BEA, Team Leader, ADMS Manager
- Hierarchical permissions (can only modify lower levels)

**Gaps**:
- No multi-factor authentication (MFA)
- No OAuth/OIDC (modern protocols)
- No fine-grained permissions (coarse profiles)
- Limited audit logging

### Network Security

**Current State**:
- Internal network only (no internet exposure)
- Firewall rules between network zones
- VPN required for remote access

**Gaps**:
- No zero-trust architecture
- No micro-segmentation
- No DDoS protection (not internet-facing)

### Data Security

**Current State**:
- Encryption at rest (database TDE)
- Encryption in transit (SQL*Net, HTTPS for web apps)
- Database auditing enabled (to be verified)

**Gaps**:
- No data masking for non-production
- No row-level security (RLS)
- Limited DLP (data loss prevention)

---

## Performance Characteristics

### Expected Performance (to be validated)

**Forms Application**:
- Form load time: 2-5 seconds
- Query response: < 1 second (indexed queries)
- Transaction commit: < 500ms
- Network latency: 10-50ms (intranet)

**Web Applications**:
- Login page load: 1-2 seconds
- Help page load: < 1 second
- API response: 100-500ms

**Database**:
- ADMSSST size: 500GB - 2TB (estimated)
- Transaction rate: 100-500 TPS (estimated)
- Concurrent users: 200-500 (estimated)

**Bottlenecks** (to be identified):
- Database I/O on AIX
- Forms server CPU (RHEL 7)
- Network bandwidth for thick client traffic

---

## Disaster Recovery & High Availability

### Current State (to be verified)

**Database**:
- Likely Oracle Data Guard (standby database)
- Backup schedule: Daily full + hourly incremental
- RPO (Recovery Point Objective): 1 hour
- RTO (Recovery Time Objective): 4 hours

**Application Servers**:
- Likely clustered (multiple Forms servers)
- Load balancer for web applications
- Failover time: 5-15 minutes

**Gaps**:
- No active-active multi-region
- Manual failover procedures (likely)
- No automated DR testing

---

## Scalability Analysis

### Current Capacity (estimated)

**User Concurrency**:
- Current: 200-500 simultaneous users
- Peak: 800 users (end of month)
- Database connections: 500-1000

**Scalability Limitations**:
- ❌ Forms server CPU-bound (cannot scale horizontally easily)
- ❌ Oracle licensing costs for scale-up
- ❌ Thick client deployment for large user base

**Cloud Comparison**:
- Azure App Service: Auto-scale 1-100+ instances
- Azure SQL Database: Elastic pools, serverless compute
- Azure CDN: Global edge caching

---

## Modernization Architecture Proposal

### Target Architecture (3-tier web)

```
┌─────────────────────────────────────────────────────────────┐
│                    User Devices                              │
│  ┌──────────────┐  ┌─────────────┐  ┌──────────────┐       │
│  │  Web Browser │  │  Mobile App │  │  Tablet      │       │
│  │  (React SPA) │  │  (iOS/Andr) │  │  (PWA)       │       │
│  └──────┬───────┘  └──────┬──────┘  └──────┬───────┘       │
└─────────┼─────────────────┼─────────────────┼───────────────┘
          │ HTTPS           │ HTTPS           │ HTTPS
┌─────────▼─────────────────▼─────────────────▼───────────────┐
│               Azure Front Door (CDN + WAF)                    │
└─────────┬────────────────────────────────────────────────────┘
          │
┌─────────▼────────────────────────────────────────────────────┐
│          Azure API Management (Gateway + Security)            │
│  ├─ OAuth 2.0 / OIDC                                         │
│  ├─ Rate limiting                                            │
│  ├─ API versioning                                           │
│  └─ Request/response transformation                          │
└─────────┬────────────────────────────────────────────────────┘
          │
┌─────────▼────────────────────────────────────────────────────┐
│                  Application Tier (Microservices)            │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐│
│  │ Employee API   │  │ Case API       │  │ Decision API   ││
│  │ (.NET 8)       │  │ (.NET 8)       │  │ (.NET 8)       ││
│  │ (Container)    │  │ (Container)    │  │ (Container)    ││
│  └────────────────┘  └────────────────┘  └────────────────┘│
│                Azure Kubernetes Service (AKS)                │
└─────────┬────────────────────────────────────────────────────┘
          │
┌─────────▼────────────────────────────────────────────────────┐
│                    Data Tier                                  │
│  ┌────────────────────┐      ┌─────────────────────────┐    │
│  │ Azure SQL Database │      │ Azure Cosmos DB         │    │
│  │ (Transactional)    │      │ (Audit logs, sessions)  │    │
│  └────────────────────┘      └─────────────────────────┘    │
└───────────────────────────────────────────────────────────────┘
```

### Benefits of Target Architecture

✅ **Cloud-native**: Auto-scaling, high availability, managed services  
✅ **Modern UI/UX**: Responsive, mobile-friendly, accessible  
✅ **API-first**: Microservices, versioned contracts  
✅ **Security**: OAuth 2.0, MFA, zero-trust  
✅ **Observability**: Application Insights, Log Analytics  
✅ **DevOps**: CI/CD, automated deployments  
✅ **Cost-effective**: Pay-as-you-go, no AIX licensing

---

## Appendices

### A. Glossary

| Term | Definition |
|------|------------|
| **ADMS** | Appeals Delivery and Management System |
| **EI** | Employment Insurance |
| **SST** | Social Security Tribunal |
| **APC** | Appeals Processing Centre |
| **SCBO** | Service Canada Benefits Officer |
| **BEA** | Business Expertise Advisor |
| **NHQ** | National Headquarters |
| **CCIS** | Canadian Case Information System |
| **CAAPS** | Canadian Appeals Processing System |

### B. Reference URLs

- Online Help: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/
- Access Admin: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/OnlineHelp.aspx?l=e&s=ADMINISTRATION&c=ACCESS

### C. Key Contacts (to be identified)

- System Owner: [TBD]
- Business Sponsor: [TBD]
- Technical Lead: [TBD]
- DBA Team: [TBD]
- Forms Developers: [TBD]

---

*Document prepared by: Marco Presta, AICOE*  
*Last updated: January 30, 2026*
