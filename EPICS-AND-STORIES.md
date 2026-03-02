# Epics and User Stories - ADMS-EI-SST

**Extracted from**: Online Help documentation + System analysis  
**Date**: January 30, 2026  
**Source**: https://intradev16-u.intra.dev/AS-SA/OnlineHelp_ADMS-EI-SST/

---

## Overview

This document contains epics and user stories extracted from the ADMS-EI-SST Access Administration documentation. These represent the **System Administration module** functionality and can serve as requirements for modernization.

**Scope**: Employee profile and access management (one of multiple system modules)

---

## Epic Summary

| Epic ID | Epic Name | User Stories | Priority |
|---------|-----------|--------------|----------|
| E01 | Initial System Access and Onboarding | 3 stories | HIGH |
| E02 | Employee Profile Management | 5 stories | HIGH |
| E03 | Profile Type Assignment | 6 stories | HIGH |
| E04 | New SCBO Management | 4 stories | MEDIUM |
| E05 | Office and Regional Management | 3 stories | MEDIUM |
| E06 | Reporting and List Management | 4 stories | LOW |
| E07 | Search and Navigation | 3 stories | MEDIUM |
| E08 | Audit and Tracking | 3 stories | HIGH |
| E09 | Profile Type Hierarchy | 3 stories | HIGH |
| E10 | Language and Bilingual Support | 2 stories | MEDIUM |

**Total**: 10 epics, 36 user stories

---

## EPIC 1: Initial System Access and Onboarding

**Description**: User access provisioning and initial setup for new ADMS users.

**Business Value**: Ensure all new employees can access the system with appropriate initial permissions.

### User Stories

#### US-E01-001: Request System Access
**As an** APC ADMS Coordinator  
**I want to** request addition of new employees to the APPS-ADMS-EI distribution list  
**So that** they can access the ADMS system

**Acceptance Criteria**:
- [ ] Can open ticket with National Service Desk
- [ ] Ticket routed to Directory Services-DCS
- [ ] Employee name added to Active Directory distribution list
- [ ] Confirmation received when access granted

**Technical Notes**:
- Integration with Service Desk ticketing system
- Active Directory API for distribution list management

---

#### US-E01-002: Automatic Initial Access
**As a** new employee  
**I want to** automatically receive enquiry-level access upon first login  
**So that** I can start viewing case information immediately without waiting for manual provisioning

**Acceptance Criteria**:
- [ ] First-time login automatically creates employee profile
- [ ] Default profile set to "Enquiries" level
- [ ] Employee can view data but not modify
- [ ] Profile creation logged for audit

**Technical Notes**:
- Auto-provisioning on first authentication
- Default role assignment logic
- Audit trail of profile creation

---

#### US-E01-003: Auto-Create Employee Profile
**As a** System Administrator  
**I want to** automatically create employee profiles when users first access ADMS  
**So that** manual profile creation is eliminated and users get immediate access

**Acceptance Criteria**:
- [ ] System detects new user authentication
- [ ] Profile created with data from Active Directory
- [ ] Email, name, office pre-populated where available
- [ ] Team Leader notified of new user for profile upgrade

**Technical Notes**:
- Integration with AD for user attributes
- Notification system for Team Leaders
- Background job for profile creation

---

## EPIC 2: Employee Profile Management

**Description**: Core CRUD (Create, Read, Update, Delete) operations for employee information and profiles.

**Business Value**: Maintain accurate employee records for access control and workload assignment.

### User Stories

#### US-E02-001: Search Employee Records
**As a** Team Leader or ADMS Manager  
**I want to** search for employees by multiple criteria (ID, name, email, phone, fax)  
**So that** I can quickly find the employee I need to manage

**Acceptance Criteria**:
- [ ] Can search by employee ID
- [ ] Can search by last name
- [ ] Can search by first name
- [ ] Can search by email address
- [ ] Can search by telephone number
- [ ] Can search by fax number
- [ ] Can use multiple criteria simultaneously
- [ ] Results returned in < 2 seconds for specific searches
- [ ] Warning displayed for overly broad searches (e.g., office-only)

**Technical Notes**:
- Indexed search on all fields
- Full-text search capability
- Search result pagination (for large result sets)

---

#### US-E02-002: Add New Employee Record
**As a** Team Leader or ADMS Manager  
**I want to** add new employee records with mandatory fields (last name, first name, email, office, language)  
**So that** employee information is complete and consistent across the system

**Acceptance Criteria**:
- [ ] Last name field is mandatory
- [ ] First name field is mandatory
- [ ] Email address field is mandatory and validated
- [ ] Office field is mandatory (dropdown selection)
- [ ] Language field is mandatory (English/French)
- [ ] Optional fields: telephone, additional telephone, fax, team leader, notes
- [ ] Cannot save record with missing mandatory fields
- [ ] Validation error messages displayed clearly
- [ ] "New employee" checkbox available
- [ ] Record saved confirmation message displayed

**Technical Notes**:
- Client-side and server-side validation
- Email format validation
- Office reference data from lookup table
- Character limits on text fields

---

#### US-E02-003: Update Employee Information
**As a** Team Leader or ADMS Manager  
**I want to** update employee information (name, phone, fax, email, team leader)  
**So that** records remain current as employee circumstances change

**Acceptance Criteria**:
- [ ] Can modify all employee fields except ID
- [ ] Changes validated before save
- [ ] Last updated timestamp automatically set
- [ ] Last updated by field automatically set to current user
- [ ] Previous values not displayed (no change history in UI)
- [ ] Save confirmation message displayed
- [ ] Changes immediately visible in search results

**Technical Notes**:
- Optimistic locking to prevent concurrent update conflicts
- Audit trail of changes in background (not displayed to user)
- Validation rules same as create

---

#### US-E02-004: Prevent Invalid Data Entry
**As a** Team Leader or ADMS Manager  
**I want to** be prevented from saving records with missing mandatory fields  
**So that** data integrity is maintained and downstream errors are avoided

**Acceptance Criteria**:
- [ ] Save button disabled when mandatory fields empty
- [ ] Validation error message displayed when attempting to save
- [ ] Invalid fields highlighted in red
- [ ] Error message specifies which fields are missing
- [ ] Cannot navigate away without saving or canceling

**Technical Notes**:
- Real-time validation on field blur
- Form-level validation on submit
- User-friendly error messages (not technical codes)

---

#### US-E02-005: Cancel Changes Before Saving
**As a** Team Leader or ADMS Manager  
**I want to** cancel changes before saving  
**So that** I can discard accidental modifications without affecting the database

**Acceptance Criteria**:
- [ ] Cancel button visible at all times
- [ ] Confirmation prompt if unsaved changes exist
- [ ] All changes discarded on cancel
- [ ] Form returns to view mode showing original values
- [ ] No database writes occur on cancel

**Technical Notes**:
- Client-side state management
- Dirty form detection
- Confirmation dialog for unsaved changes

---

## EPIC 3: Profile Type Assignment and Authorization

**Description**: Managing user access levels through profile assignments with hierarchical permissions.

**Business Value**: Ensure users have appropriate access rights for their role while maintaining security and authorization hierarchy.

### User Stories

#### US-E03-001: Assign Profile Types with Hierarchy Enforcement
**As a** Team Leader or ADMS Manager  
**I want to** assign profile types from a restricted pick list (only profiles below my level)  
**So that** authorization hierarchy is enforced and I cannot grant privileges higher than mine

**Acceptance Criteria**:
- [ ] Pick list only shows profile types below user's current level
- [ ] Team Leader APC can assign: Clerk, SCBO, BEA (not Team Leader or Manager)
- [ ] ADMS Manager can assign all profiles except Manager level
- [ ] Attempt to assign higher profile returns error
- [ ] Profile assignment immediately active after save

**Technical Notes**:
- Role hierarchy table defining levels
- Dynamic filtering of available profiles based on user's role
- Server-side authorization check on save

---

#### US-E03-002: Add Multiple Profile Types Over Time
**As a** Team Leader or ADMS Manager  
**I want to** add multiple profile types to an employee over time  
**So that** users can have different roles as their career progresses without losing history

**Acceptance Criteria**:
- [ ] Can add new profile to employee with existing profile
- [ ] Both profiles visible in profile list
- [ ] Only one profile can be active at a time
- [ ] Expired profiles displayed below active profiles
- [ ] Profile history maintained for audit

**Technical Notes**:
- Profile table with employee_id FK, start_date, expiry_date
- Active profile: expiry_date IS NULL
- Sort order: active profiles first, then by start_date DESC

---

#### US-E03-003: Automatic Profile Expiry on New Assignment
**As a** Team Leader or ADMS Manager  
**I want to** see the system automatically expire previous profiles when adding a new one  
**So that** only one profile is active at a time without manual intervention

**Acceptance Criteria**:
- [ ] Adding new profile triggers expiry of current active profile
- [ ] System message displayed: "The previous profile will be expired"
- [ ] Expiry date set to current date
- [ ] New profile becomes active (no expiry date)
- [ ] Both profiles visible in history
- [ ] Transaction is atomic (both changes succeed or both fail)

**Technical Notes**:
- Database trigger or application logic for auto-expiry
- Transaction management for atomicity
- Timestamp for expiry date (not just date)

---

#### US-E03-004: Manual Profile Expiry
**As a** Team Leader or ADMS Manager  
**I want to** manually add expiry dates to profiles  
**So that** I can deactivate user access without deleting records (e.g., employee leaves team)

**Acceptance Criteria**:
- [ ] Can enter date in expiry date field
- [ ] Profile with expiry date is no longer "active"
- [ ] Expired profile moves to bottom of profile list
- [ ] User's name removed from "Assign to" pick lists in workload screens
- [ ] Cannot set expiry date in the future beyond employee departure
- [ ] Expiry date must be >= profile start date

**Technical Notes**:
- Date picker control for expiry date
- Validation: expiry_date >= start_date
- Cascade: update "Assign to" pick lists dynamically

---

#### US-E03-005: Reactivate Expired Profile
**As a** Team Leader or ADMS Manager  
**I want to** reactivate profiles by removing expiry dates  
**So that** returning employees can regain access without creating duplicate profiles

**Acceptance Criteria**:
- [ ] Can clear expiry date field
- [ ] If no other active profiles exist, profile becomes active
- [ ] Profile moves back to top of list
- [ ] User reappears in "Assign to" pick lists
- [ ] If another active profile exists, error message (ADMS 828) displayed
- [ ] Error: "Only a single profile can be active. Please enter an expiry date beside all existing profiles except for the one that you wish to keep active, before saving or exiting this screen."

**Technical Notes**:
- Validation: count of active profiles after change
- Error code ADMS 828 for multiple active profiles
- Prevent save/exit until only one active profile

---

#### US-E03-006: Enforce Single Active Profile
**As a** System Administrator  
**I want to** enforce only one active profile per user at all times  
**So that** authorization conflicts are prevented and access rights are unambiguous

**Acceptance Criteria**:
- [ ] Database constraint: one active profile per employee
- [ ] Application validation before save
- [ ] Error message (ADMS 828) if attempting to activate multiple profiles
- [ ] Cannot exit screen until violation resolved
- [ ] Cannot save record until violation resolved

**Technical Notes**:
- Check constraint in database: COUNT(active profiles) <= 1
- Application-level validation before commit
- User-friendly error message with resolution steps

---

## EPIC 4: New Service Canada Benefits Officer (SCBO) Management

**Description**: Special handling for new officers during their monitoring/training period with restricted permissions.

**Business Value**: Ensure quality control during training period by routing new SCBO work for review.

### User Stories

#### US-E04-001: Mark New Employees for Monitoring
**As a** Team Leader or ADMS Manager  
**I want to** mark employees with a "New employee" checkbox  
**So that** SCBOs under monitoring have appropriate restrictions during their training period

**Acceptance Criteria**:
- [ ] "New employee" checkbox visible on employee profile
- [ ] Checkbox auto-filled when adding SCBO profile for first time
- [ ] Team Leader verifies checkbox is checked when adding new SCBO
- [ ] Checkbox state stored in database
- [ ] Checkbox state affects available actions in case workflows

**Technical Notes**:
- Boolean field: is_new_employee
- Auto-set on first SCBO profile assignment
- Used in business logic for workflow restrictions

---

#### US-E04-002: Restrict New SCBO Actions
**As a** Team Leader  
**I want to** prevent new SCBOs from actioning denied delayed reconsideration requests  
**So that** quality control is maintained during training and errors are minimized

**Acceptance Criteria**:
- [ ] When "New employee" checked, certain action buttons greyed out
- [ ] Denied delayed request action unavailable
- [ ] Administrative Review Decision fields unavailable
- [ ] "Date sent to BEA for Review" checkbox displayed instead
- [ ] Restrictions apply to specific case types only
- [ ] Error message if attempting restricted action via API/backdoor

**Technical Notes**:
- Conditional rendering based on is_new_employee flag
- Server-side authorization check for restricted actions
- Specific case types affected (delayed denied reconsiderations)

---

#### US-E04-003: Route New SCBO Work for Review
**As a** new SCBO  
**I want to** see a "Date sent to BEA for Review" checkbox instead of decision fields  
**So that** my work is automatically routed to a Business Expertise Advisor for review

**Acceptance Criteria**:
- [ ] Checkbox displayed in place of decision fields
- [ ] Checking box completes my action on case
- [ ] Case status changes to "Pending BEA Review"
- [ ] BEA receives notification of case awaiting review
- [ ] Case appears in BEA work queue
- [ ] I cannot modify case after sending for review

**Technical Notes**:
- Workflow state transition: In Progress → Pending BEA Review
- Notification service for BEA alerts
- Work queue filtering by status

---

#### US-E04-004: Remove Monitoring Status
**As a** Team Leader or BEA  
**I want to** uncheck the "New employee" box after the monitoring period  
**So that** SCBOs gain full access rights and can work independently

**Acceptance Criteria**:
- [ ] Can uncheck "New employee" checkbox
- [ ] Change effective immediately after save
- [ ] SCBO gains access to previously restricted actions
- [ ] Decision fields replace "Date sent to BEA for Review" checkbox
- [ ] Change logged in audit trail
- [ ] Team Leader determines when monitoring period complete (no auto-calculation)

**Technical Notes**:
- Manual toggle by Team Leader or BEA only
- No automated time-based removal
- Audit log entry with timestamp and user

---

## EPIC 5: Office and Regional Management

**Description**: Managing users across different offices and regions with regional organization.

**Business Value**: Support distributed organization across 4 regional APCs and National Headquarters.

### User Stories

#### US-E05-001: Assign Employees to Offices
**As a** Team Leader or ADMS Manager  
**I want to** assign employees to offices (Atlantic-APC, Ontario-APC, Quebec-APC, Western-APC, NHQ)  
**So that** regional organization is maintained and work can be routed appropriately

**Acceptance Criteria**:
- [ ] Office field is mandatory dropdown
- [ ] Available options:
  - Atlantic-APC
  - Ontario-APC
  - Quebec-APC
  - Western-APC
  - NHQ (National Headquarters)
- [ ] Cannot save record without office selection
- [ ] Office change logged in audit trail
- [ ] Reports can filter by office

**Technical Notes**:
- Office reference table
- FK constraint: employee.office_id → offices.office_id
- Audit trigger on office change

---

#### US-E05-002: Assign Team Leaders to Employees
**As a** Team Leader or ADMS Manager  
**I want to** assign team leaders to employees  
**So that** reporting relationships are tracked and workload can be reviewed by supervisors

**Acceptance Criteria**:
- [ ] Team Leader field is optional dropdown
- [ ] Dropdown populated with employees having "Team Leader" profile
- [ ] Filtered by office (only show Team Leaders in same office)
- [ ] Team Leader email auto-populated when Team Leader selected
- [ ] Can leave blank if no Team Leader assigned
- [ ] Employees can see their Team Leader's name/email

**Technical Notes**:
- Self-referential FK: employee.team_leader_id → employee.employee_id
- Dynamic filtering based on profile type and office
- Denormalized email field for performance (or join on display)

---

#### US-E05-003: Enforce Authorization Boundaries by Level
**As a** Team Leader APC  
**I want to** only modify employees at levels below Team Leader  
**So that** authorization boundaries are enforced and I cannot escalate my own privileges

**Acceptance Criteria**:
- [ ] Can modify profiles: Clerk, SCBO, BEA
- [ ] Cannot modify profiles: Team Leader, ADMS Manager
- [ ] Attempting to modify higher profile returns error
- [ ] Can view higher profiles (read-only)
- [ ] ADMS Manager can modify all profiles except other ADMS Managers

**Technical Notes**:
- Role hierarchy level comparison
- Server-side authorization on update
- Read-only rendering for profiles above user's level

---

## EPIC 6: Reporting and List Management

**Description**: Generating reports and lists of active employees for auditing and management.

**Business Value**: Provide visibility into who has access to the system and facilitate access audits.

### User Stories

#### US-E06-001: Print Active Employees by Profile Type
**As a** Team Leader or ADMS Manager  
**I want to** print lists of active employees by profile type  
**So that** I can audit who has access at each level

**Acceptance Criteria**:
- [ ] "Print Active Employees" button available
- [ ] Filter by profile type:
  - Clerk - APC
  - Clerk - NHQ
  - Legal Services
  - Senior Appeals Officer
  - Service Canada Benefits Officer
  - Team Leader - APC
  - All (no filter)
- [ ] Only employees with no expiry date (active profiles) included
- [ ] Output includes: Name, Email, Office, Profile Type, Team Leader

**Technical Notes**:
- Crystal Reports or SSRS template
- Query: SELECT ... WHERE expiry_date IS NULL AND profile_type = :filter
- Report generation service

---

#### US-E06-002: Print Active Employees by Office
**As a** Team Leader or ADMS Manager  
**I want to** print lists of active employees by office  
**So that** I can review regional staffing and access levels

**Acceptance Criteria**:
- [ ] Filter by office:
  - Atlantic – Appeal Processing Centre
  - Ontario – Appeal Processing Centre
  - Quebec – Appeal Processing Centre
  - Western-T - Appeal Processing Centre
  - NHQ
  - All (no filter)
- [ ] If left blank, defaults to "All"
- [ ] Sorted by office, then by last name

**Technical Notes**:
- Report parameters: office, profile_type
- Default values: All
- Group by office in report layout

---

#### US-E06-003: Export Reports in Multiple Formats
**As a** Team Leader or ADMS Manager  
**I want to** export employee lists in spreadsheet or PDF format  
**So that** I can share information with stakeholders or perform further analysis

**Acceptance Criteria**:
- [ ] Format selection: Spreadsheet (Excel) or PDF
- [ ] Excel format: Includes all columns, sortable, filterable
- [ ] PDF format: Formatted for printing, includes header/footer
- [ ] File download initiated immediately after generation
- [ ] Filename includes timestamp and filters applied

**Technical Notes**:
- Export library (EPPlus for Excel, iTextSharp for PDF)
- Streaming for large result sets
- Secure temporary file storage

---

#### US-E06-004: Filter Reports by All Profiles and Offices
**As a** Team Leader or ADMS Manager  
**I want to** filter reports by "All" profiles and offices  
**So that** I can get comprehensive views without running multiple reports

**Acceptance Criteria**:
- [ ] "All" option available in both dropdowns
- [ ] Selecting "All" includes all records (no filter)
- [ ] Blank selection defaults to "All"
- [ ] Report shows filters applied in header
- [ ] Can combine: specific profile + All offices, or All profiles + specific office

**Technical Notes**:
- SQL: WHERE (profile_type = :param OR :param = 'All')
- Report header dynamic based on selections
- Parameter handling for NULL/"All" equivalence

---

## EPIC 7: Search and Navigation

**Description**: Efficient search capabilities for finding employee records quickly.

**Business Value**: Reduce time spent locating employee records for updates.

### User Stories

#### US-E07-001: Multi-Criteria Employee Search
**As a** Team Leader or ADMS Manager  
**I want to** search using any combination of employee fields  
**So that** I can find records with partial information (e.g., only know first name)

**Acceptance Criteria**:
- [ ] Can enter multiple criteria simultaneously
- [ ] Empty fields ignored in search (AND logic)
- [ ] Partial match supported (wildcards)
- [ ] Search button disabled until at least one criterion entered
- [ ] Results displayed in grid with pagination
- [ ] Can click result to open employee record

**Technical Notes**:
- Dynamic SQL generation based on populated fields
- LIKE '%value%' for partial matching
- Pagination: 25 records per page
- Index optimization for common search patterns

---

#### US-E07-002: Broad Search Warning
**As a** Team Leader or ADMS Manager  
**I want to** be warned about overly broad searches (e.g., office-only)  
**So that** I can narrow criteria for better results and avoid performance issues

**Acceptance Criteria**:
- [ ] Warning message if search will return > 100 results
- [ ] Message: "Your search may return a large number of records. Consider narrowing your search for better results."
- [ ] Option to proceed anyway or refine search
- [ ] Warning does not block search, only informs
- [ ] Count query runs before main search query

**Technical Notes**:
- COUNT(*) query with same filters
- Threshold: 100 records
- User confirmation dialog
- Timeout protection for large result sets

---

#### US-E07-003: Unsearchable Fields Indication
**As a** Team Leader or ADMS Manager  
**I want to** see which fields cannot be searched (Last updated, Last updated by)  
**So that** I use appropriate search criteria and don't waste time entering invalid searches

**Acceptance Criteria**:
- [ ] "Last updated" field not included in search form
- [ ] "Last updated by" field not included in search form
- [ ] Tooltip explains why fields not searchable
- [ ] Search form only shows searchable fields
- [ ] Help text lists all searchable fields

**Technical Notes**:
- UI design: Hide non-searchable fields
- Inline help icons with tooltips
- Consistent with search functionality implementation

---

## EPIC 8: Audit and Tracking

**Description**: System tracking of changes and user actions for accountability.

**Business Value**: Maintain audit trail for compliance and troubleshooting.

### User Stories

#### US-E08-001: Track Who Last Updated Records
**As a** System Administrator  
**I want to** automatically track who last updated each record  
**So that** accountability is maintained and I can identify who made changes

**Acceptance Criteria**:
- [ ] "Last updated by" field automatically populated on save
- [ ] Value set to current authenticated user
- [ ] Field is read-only (cannot be manually edited)
- [ ] Displayed on employee record view
- [ ] Stored as user ID (not name, for consistency if name changes)

**Technical Notes**:
- Trigger or application logic on UPDATE
- Current user from session context
- FK to users table (or employee table)

---

#### US-E08-002: Track When Records Last Updated
**As a** System Administrator  
**I want to** automatically track when records were last updated  
**So that** change history is preserved and I can identify stale records

**Acceptance Criteria**:
- [ ] "Last updated" field automatically populated on save
- [ ] Value set to current timestamp (date + time)
- [ ] Field is read-only (cannot be manually edited)
- [ ] Displayed on employee record view
- [ ] Timezone: Server timezone (EST/EDT)

**Technical Notes**:
- Trigger or application logic on UPDATE
- TIMESTAMP data type
- Server-side timestamp (not client-provided)

---

#### US-E08-003: Prevent Manual Edit of Audit Fields
**As a** System Administrator  
**I want to** ensure audit fields cannot be manually edited  
**So that** audit trail integrity is guaranteed and tampering is prevented

**Acceptance Criteria**:
- [ ] "Last updated" field greyed out (disabled)
- [ ] "Last updated by" field greyed out (disabled)
- [ ] API rejects attempts to set these fields manually
- [ ] Database triggers enforce values even if API bypassed
- [ ] Error message if attempting to edit: "Audit fields cannot be manually modified"

**Technical Notes**:
- UI: disabled attribute on form fields
- API: Ignore values provided by client
- Database: Triggers always set values
- Defense in depth: UI + API + DB protection

---

## EPIC 9: Profile Type Hierarchy

**Description**: Managing different levels of access and capabilities with hierarchical permissions.

**Business Value**: Ensure appropriate access rights for each role while maintaining security.

### User Stories

#### US-E09-001: Support Multiple Profile Types
**As a** System Administrator  
**I want to** support multiple profile types (Clerk-APC, Clerk-NHQ, Legal Services, Senior Appeals Officer, SCBO, Team Leader-APC, ADMS Manager)  
**So that** different roles have appropriate access to system functions

**Acceptance Criteria**:
- [ ] Profile types table includes all required types:
  - Clerk - APC
  - Clerk - NHQ
  - Legal Services
  - Senior Appeals Officer
  - Service Canada Benefits Officer (SCBO)
  - Team Leader - APC
  - ADMS Manager
  - Business Expertise Advisor (BEA)
- [ ] Each profile type has hierarchy level defined
- [ ] Each profile type has permissions defined
- [ ] Can add new profile types without code changes (data-driven)

**Technical Notes**:
- profile_types reference table
- Hierarchy level: 1 (lowest) to 10 (highest)
- Permissions stored as JSON or separate permissions table

---

#### US-E09-002: Hide Expired Profiles from Assignment Lists
**As a** Team Leader or ADMS Manager  
**I want to** hide users with expired profiles from assignment pick lists  
**So that** only active users are assignable to work and I don't accidentally assign to inactive staff

**Acceptance Criteria**:
- [ ] "Assign to" dropdowns only show active profiles (no expiry date)
- [ ] When user profile expires, immediately removed from pick lists
- [ ] When user profile reactivated, immediately added back to pick lists
- [ ] Pick lists sorted alphabetically by last name
- [ ] Pick lists include user's profile type in parentheses

**Technical Notes**:
- Dynamic query: WHERE expiry_date IS NULL
- Cascade: Updates to profile expiry trigger pick list refresh
- Caching strategy for pick list data (invalidate on profile change)

---

#### US-E09-003: Sort Expired Profiles to Bottom of List
**As a** Team Leader or ADMS Manager  
**I want to** see expired profiles moved to the bottom of profile lists  
**So that** active profiles are easily visible and I focus on current assignments

**Acceptance Criteria**:
- [ ] Profile list sorted by:
  1. Active profiles first (expiry_date IS NULL)
  2. Expired profiles second (expiry_date IS NOT NULL)
  3. Within each group, alphabetical by profile type
- [ ] Visual distinction: expired profiles greyed out or marked with icon
- [ ] Hover tooltip on expired profile shows expiry date
- [ ] Expired profiles still visible for history/audit

**Technical Notes**:
- SQL: ORDER BY (CASE WHEN expiry_date IS NULL THEN 0 ELSE 1 END), profile_type
- CSS: .expired-profile { opacity: 0.5; }
- UI: Icon indicator for expired (e.g., clock icon)

---

## EPIC 10: Language and Bilingual Support

**Description**: Supporting English and French language requirements for Canadian government.

**Business Value**: Comply with Official Languages Act and serve employees in language of choice.

### User Stories

#### US-E10-001: Record Employee Language Preference
**As a** Team Leader or ADMS Manager  
**I want to** record employee language preference (English/French)  
**So that** work can be assigned appropriately and communications sent in correct language

**Acceptance Criteria**:
- [ ] Language field is mandatory dropdown
- [ ] Options: English, French
- [ ] Used for:
  - Assigning cases in employee's language
  - Sending notifications in employee's language
  - Generating reports in employee's language
- [ ] Can be changed at any time
- [ ] Change takes effect immediately

**Technical Notes**:
- Language code: 'E' (English), 'F' (French)
- Stored in employee.language field
- Used in workload assignment logic and notification templates

---

#### US-E10-002: Support Bilingual User Interface
**As a** System User  
**I want to** view the interface in English or French  
**So that** I can work in my preferred official language

**Acceptance Criteria**:
- [ ] Language toggle available in header (EN | FR)
- [ ] All UI text translated (labels, buttons, messages, help text)
- [ ] User language preference saved in profile
- [ ] Forms documentation available in both languages
- [ ] Error messages displayed in user's language
- [ ] Reports generated in user's language

**Technical Notes**:
- Resource files for translations
- Browser locale detection as default
- User preference stored in session
- Content-Language HTTP header
- Bilingual database content where required

---

## Cross-Cutting Concerns (Technical Stories)

### Security and Integration

#### US-CC-001: Active Directory Integration
**As a** System Administrator  
**I want to** integrate with Active Directory (APPS-ADMS-EI distribution list)  
**So that** authentication is centralized and users managed in single source of truth

**Acceptance Criteria**:
- [ ] Authentication via AD credentials
- [ ] AD group membership determines initial access
- [ ] User attributes synced from AD (name, email)
- [ ] Password policy enforced by AD
- [ ] Single Sign-On (SSO) supported

---

#### US-CC-002: Validation Rules Enforcement
**As a** System Administrator  
**I want to** validate mandatory fields at save time  
**So that** data quality is enforced and downstream errors prevented

**Acceptance Criteria**:
- [ ] Client-side validation provides immediate feedback
- [ ] Server-side validation prevents API bypass
- [ ] Database constraints enforce rules at lowest level
- [ ] Validation rules centrally defined (not duplicated across layers)
- [ ] Validation errors return specific field names and reasons

---

#### US-CC-003: User-Friendly Error Messages
**As a** System Administrator  
**I want to** provide clear error messages (e.g., ADMS 828 for multiple active profiles)  
**So that** users can correct issues without technical support

**Acceptance Criteria**:
- [ ] Error codes defined (e.g., ADMS 828)
- [ ] Error messages explain problem and solution
- [ ] No technical jargon (no stack traces, SQL errors)
- [ ] Actionable: Tell user what to do to fix
- [ ] Consistent format across all errors

---

#### US-CC-004: Link to Workload Modules
**As a** System Administrator  
**I want to** have employee profiles linked to workload assignment modules (Reconsideration, SST)  
**So that** access controls cascade properly and workload routing works correctly

**Acceptance Criteria**:
- [ ] Active profile determines available actions in case modules
- [ ] "Assign to" pick lists filtered by active profile and office
- [ ] Profile changes immediately affect workload assignment
- [ ] Cannot assign work to users with expired profiles
- [ ] Workload reports show employee profile type

---

## Story Estimation Summary

### Story Points (Estimated)

| Epic | Total Story Points | Complexity |
|------|-------------------|------------|
| E01: Onboarding | 8 SP | Medium |
| E02: Profile Management | 13 SP | Medium |
| E03: Profile Assignment | 21 SP | High |
| E04: New SCBO Management | 13 SP | High |
| E05: Office Management | 8 SP | Low |
| E06: Reporting | 13 SP | Medium |
| E07: Search | 8 SP | Medium |
| E08: Audit | 5 SP | Low |
| E09: Hierarchy | 8 SP | Medium |
| E10: Language | 13 SP | High |
| Cross-Cutting | 13 SP | High |

**Total Estimated Story Points**: ~123 SP

**Estimated Development Time** (assuming 2 SP per developer-day):
- Traditional: 61.5 developer-days (~12 weeks with 1 developer)
- With GitHub Copilot (33% faster): 41 developer-days (~8 weeks with 1 developer)

---

## Prioritization

### Must Have (MVP - Minimum Viable Product)
- E01: Onboarding
- E02: Profile Management
- E03: Profile Assignment
- E08: Audit

### Should Have (Iteration 2)
- E04: New SCBO Management
- E07: Search
- E09: Hierarchy

### Could Have (Iteration 3)
- E05: Office Management
- E10: Language

### Won't Have (Future)
- E06: Reporting (use existing Cognos, migrate later)

---

## Dependencies

```
E01 (Onboarding)
  ↓
E02 (Profile Management)
  ↓
E03 (Profile Assignment) ← E09 (Hierarchy)
  ↓
E04 (New SCBO) ← E03
  ↓
E05 (Office) ← E02
  ↓
E07 (Search) ← E02

E08 (Audit) → All epics (cross-cutting)
E10 (Language) → All epics (cross-cutting)
```

---

## Acceptance Testing Scenarios

### Scenario 1: New Employee Onboarding
1. ADMS Coordinator opens ticket to add John Smith to APPS-ADMS-EI
2. Directory Services adds John Smith to distribution list
3. John Smith logs in for first time
4. System auto-creates profile with "Enquiries" level
5. John Smith can view cases but not modify
6. Team Leader assigns SCBO profile to John Smith
7. Team Leader checks "New employee" box
8. John Smith can now work cases but cannot action denied delayed requests
9. After 3 months, Team Leader unchecks "New employee" box
10. John Smith has full SCBO access

### Scenario 2: Profile Management Lifecycle
1. Team Leader searches for employee Jane Doe
2. Team Leader updates Jane's phone number
3. System saves change and updates "Last updated" fields
4. Team Leader adds "Business Expertise Advisor" profile
5. System automatically expires previous "SCBO" profile
6. Jane's new BEA profile is now active
7. Jane appears in BEA pick lists, removed from SCBO pick lists
8. 6 months later, Jane leaves team
9. Team Leader adds expiry date to BEA profile
10. Jane no longer appears in any assignment pick lists

---

*This document will be updated as additional modules are analyzed*

---

**Created by**: Marco Presta, AICOE  
**Last updated**: January 30, 2026  
**Source**: ADMS-EI-SST Online Help System
