
# Day 01 | Active Directory Baseline and Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | SOC-AD-001 |
| Exercise | Active Directory Baseline and Identity Inventory |
| Date | 2026-11-03 |
| Environment | Proxmox VE, Windows Server 2022, Windows 11 |
| Domain Controller | DC01 |
| Domain | corp.local |
| Activity Type | Baseline verification and identity inventory |
| Status | Completed |
| Disposition | Baseline documented. No security incident assessed. |

## 1. Initial Objective

Establish a reliable Active Directory baseline before performing identity administration, access management, or security investigations.

This exercise was a planned inventory activity, not a response to a security alert.

The objective was to document the existing domain environment, verify the Active Directory Domain Services service status, and identify the user, computer, and organizational unit objects available for subsequent administrative and security investigations.

## 2. Systems and Evidence Sources

- DC01 running Windows Server 2022
- Active Directory Users and Computers (ADUC)
- Administrative Windows PowerShell
- Existing Active Directory organizational units and objects

PowerShell was used for system and service verification.

ADUC was used to inspect the existing directory structure, organizational units, user accounts, and computer objects.

The observations documented below represent the environment at the time of inspection.

## 3. Actions and Verified Results

| Action | Verified Result |
| --- | --- |
| Execute `hostname` | dc01 |
| Query computer domain | corp.local |
| Execute `Get-Service NTDS` | Running |
| Inspect Admins OU | Empty |
| Inspect CorpOps Employees OU | Daniel Carter account present |
| Inspect CorpOps IT OU | Empty |
| Inspect CorpOps ServiceAccounts OU | Empty |
| Inspect CorpOps Workstations OU | Windows client computer object present |
| Inspect Domain Controllers OU | DC01 present, displayed as GC |
| Inspect default Computers container | Empty |
| Inspect default Users container | Built-in accounts and security groups visible |

These findings represent the observed environment at the time of inspection.

The completed checks established an initial domain identity and computer inventory.

They did not constitute a comprehensive Active Directory health, authentication, or security assessment.

## 4. Technical Evidence and Findings

### Finding 01: Active Directory Domain Services Status

**Verification method:** Administrative PowerShell query using `Get-Service NTDS`.

**Observed:** The NTDS service reported a Running status.

**Interpretation:** Active Directory Domain Services was running at the time of verification.

**Limitation:** The service status does not independently establish successful authentication, DNS functionality, replication health, or complete domain availability.

### Finding 02: Organizational Unit Structure

**Verification method:** Active Directory Users and Computers.

**Observed:** The existing domain organizational structure was accessible through ADUC.

The following organizational units were inspected:

- Admins
- CorpOps Employees
- CorpOps IT
- CorpOps ServiceAccounts
- CorpOps Workstations
- Domain Controllers

**Interpretation:** The observed structure establishes a starting reference for subsequent directory administration and investigation of structural changes.

**Limitation:** The inspection did not establish whether administrative delegation, permissions, or Group Policy inheritance were configured correctly.

### Finding 03: Employee Account Inventory

**Verification method:** Inspection of the CorpOps Employees OU using ADUC.

**Observed:** The Daniel Carter account was present in the Employees OU.

**Interpretation:** The account forms part of the initial identity inventory and provides a known reference for subsequent account administration and authentication exercises.

**Limitation:** Account presence does not establish successful authentication, effective permissions, group membership, or authorized access.

### Finding 04: Workstation Computer Account

**Verification method:** Inspection of the CorpOps Workstations OU using ADUC.

**Observed:** A Windows client computer object was present.

**Interpretation:** The directory object provides an initial reference for subsequent computer account and domain membership investigations.

**Limitation:** The presence of a computer object does not establish that the endpoint was online, that its secure channel was healthy, or that domain authentication was successful.

### Finding 05: Default Active Directory Containers

**Verification method:** Inspection using ADUC.

**Observed:**

- The default Computers container was empty.
- The default Users container contained built-in accounts and security groups.
- The Domain Controllers OU contained DC01, displayed as GC.

**Interpretation:** These observations establish additional inventory context for the domain at the time of inspection.

**Limitation:** Object visibility alone does not validate account configuration, permissions, authentication behavior, or domain controller health.

## 5. Analyst Assessment

### Observed

- The domain controller identified itself as dc01.
- The computer reported membership in corp.local.
- The NTDS service reported a Running status.
- Existing user and computer objects were visible in ADUC.
- The Daniel Carter account was present in the CorpOps Employees OU.
- A Windows client computer object was present in the CorpOps Workstations OU.
- Several organizational units contained no objects at the time of inspection.
- The default Computers container was empty.
- The default Users container contained built-in accounts and security groups.
- DC01 was visible in the Domain Controllers OU and displayed as GC.

These observations document the initial directory state examined during Day 01.

### Correlated

PowerShell verification and ADUC inspection provided complementary observations of the domain controller configuration and accessible directory structure.

The hostname and domain information identified the system under review.

The NTDS service query established the reported service state.

ADUC inspection provided visibility into existing organizational units, user accounts, and computer objects.

Together, these findings support the establishment of an initial Active Directory baseline.

They do not independently establish complete domain health or validate all authentication and authorization functions.

### Interpretation

The collected information establishes an initial identity and computer inventory for future comparison.

The documented structure can support subsequent investigations involving:

- User account creation or deletion
- Account movement between organizational units
- Security group membership changes
- Computer account administration
- Unexpected changes to directory objects

An empty organizational unit is an inventory observation.

It does not automatically indicate a security failure or misconfiguration.

Similarly, a running NTDS service and visible directory objects do not establish that all Active Directory services and security controls are functioning correctly.

No evidence collected during this exercise established malicious activity or a security incident.

### Unknown

The following areas were not fully validated during Day 01:

- Complete security group memberships
- End-to-end DNS functionality
- Authentication success for all identities
- Group Policy application
- Full domain connectivity
- Active Directory replication health, if applicable
- Effective access permissions and administrative delegation
- Endpoint connectivity and computer account trust health

These areas require separate verification before conclusions can be made.

## 6. Actions and Changes

This was a read-only baseline exercise.

The activities consisted of:

- Inspecting the system hostname and domain information
- Checking the NTDS service status
- Reviewing existing Active Directory organizational units
- Identifying existing user and computer objects
- Documenting the observed directory structure

No new user accounts, security groups, organizational units, or computer objects were created as part of Day 01.

No remediation or detection tuning was performed.

No configuration changes were documented during the exercise.

## 7. Classification and Disposition

**Classification:** Planned Active Directory baseline activity.

**Disposition:** Inventory completed and documented.

**Security Conclusion:** No incident determination was required because this exercise was not triggered by a security alert.

The observations were consistent with the expected purpose of a baseline inventory exercise.

The absence of an identified incident during this exercise does not establish that the environment is free from threats.

No malicious activity, unauthorized change, or compromise was established by the checks performed.

## 8. Verification and Evidence Gaps

The completed system and directory checks support the baseline observations documented in this investigation.

However, the following capabilities were not comprehensively validated:

- Authentication behavior
- DNS functionality
- Security group membership
- Group Policy application
- Access permissions
- Domain connectivity
- Domain controller health beyond the observed NTDS service state
- Computer account trust and endpoint connectivity

These items remain outside the completed Day 01 scope.

No additional verification results should be inferred from the available observations.

The documented baseline should therefore be treated as an initial inventory reference rather than a complete assessment of domain security or operational health.

## 9. Handover Notes

The next analyst should use this baseline when investigating identity changes or continuing the approved Active Directory exercises.

### Baseline Reference

| Item | Documented State |
| --- | --- |
| Domain controller | DC01 |
| Domain | corp.local |
| NTDS service | Running |
| Employee account | Daniel Carter present |
| Workstation computer object | Present in CorpOps Workstations OU |
| Organizational structure | Inspected and documented |
| Default Computers container | Empty |
| Default Users container | Built-in accounts and security groups present |
| Initial inventory | Completed |

### Investigation Considerations

When examining subsequent directory changes, compare the current state against the Day 01 observations.

Determine:

- Which directory object changed.
- What property or configuration changed.
- Whether the change was expected.
- Whether supporting telemetry or administrative records establish the actor responsible.
- Whether the change requires further security investigation.

A difference from the baseline should be investigated in context rather than automatically classified as malicious.

### Outstanding Verification

Authentication, DNS, Group Policy, access permissions, domain connectivity, and applicable replication health were not comprehensively validated.

Previously completed Day 01 checks should not be repeated unless a new investigation, configuration change, or troubleshooting requirement makes revalidation necessary.

## 10. Next Action

Proceed to Day 02 under the approved 90 Days of SOC Tier 1 and Tier 2 Mastery roadmap.

Use the documented Day 01 findings as the starting reference.

Any additional checks should follow the approved Day 02 scope and must not be represented as completed Day 01 work.

## Documentation Principle

Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.

