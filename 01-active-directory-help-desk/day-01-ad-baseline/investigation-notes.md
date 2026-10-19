
# Day 01 | Active Directory Baseline — Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | SOC-AD-001 |
| Exercise | Active Directory Baseline & Identity Inventory |
| Date | 2026-10-17 |
| Environment | Proxmox VE, Windows Server 2022, Windows 11 |
| Domain controller | DC01 |
| Domain | corp.local |
| Activity type | Baseline verification and identity inventory |
| Status | Completed |
| Disposition | Baseline documented; no security incident assessed |

## 1. Initial Objective

Establish a reliable Active Directory baseline before performing identity administration, access management, or security investigations.

The exercise was a planned inventory activity, not a response to a security alert.

## 2. Systems and Evidence Sources

- DC01 running Windows Server 2022
- Active Directory Users and Computers (ADUC)
- Administrative Windows PowerShell
- Existing Active Directory organizational units and objects
- Genuine screenshots captured during the exercise

## 3. Actions and Verified Results

| Action | Verified result |
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

These results describe the environment at the time of inspection.

## 4. Evidence References

### E01 — Active Directory Service

![NTDS service running](evidence/day-01-ntds-service-running.png)

**Observed:** The NTDS service reported a Running status.

### E02 — Organizational Structure

![Active Directory OU structure](evidence/day-01-ad-ou-structure.png)

**Observed:** The existing domain organizational structure was accessible through ADUC.

### E03 — Employee Identity

![Employee account inventory](evidence/day-01-employee-account-inventory.png)

**Observed:** An existing employee identity was present in the Employees OU.

### E04 — Workstation Identity

![Workstation computer account](evidence/day-01-workstation-computer-account.png)

**Observed:** A Windows client computer object was present in the Workstations OU.

## 5. Analyst Assessment

### Observed

- The domain controller identified itself as dc01.
- The computer reported membership in corp.local.
- Active Directory Domain Services was running.
- Existing user and computer objects were visible in ADUC.
- Several organizational units contained no objects at the time of inspection.

### Correlated

PowerShell verification and ADUC inspection provided complementary evidence of the domain controller's configuration and accessible directory structure.

### Interpretation

The collected information establishes an initial identity and computer inventory for future comparison.

An empty OU is an inventory observation, not evidence of a security failure.

### Unknown

- Complete security group memberships
- End-to-end DNS functionality
- Authentication success for all identities
- Group Policy application
- Full domain connectivity and replication health

These were not established by this exercise.

## 6. Actions and Changes

This was a read-only baseline exercise.

No new user accounts, security groups, organizational units, or computer objects were created as part of Day 01.

No remediation or detection tuning was performed.

## 7. Classification and Disposition

**Classification:** Planned Active Directory baseline activity.

**Disposition:** Inventory completed and documented.

**Security conclusion:** No incident determination was required. The absence of an alert or identified incident during this exercise does not establish that the environment is free from threats.

## 8. Verification and Evidence Gaps

The available screenshots support the documented service and directory observations.

Additional validation of authentication, DNS, access permissions, and Group Policy remains part of later exercises.

## 9. Handover Notes

The next analyst should use this baseline when investigating identity changes or continuing the approved Active Directory exercises.

Do not repeat the completed Day 01 inventory unless a new investigation or configuration change makes revalidation necessary.

## 10. Next Action

Proceed to Day 02 under the approved 90-day SOC mastery roadmap.

Use the documented Day 01 findings as the starting reference.

---

**Documentation principle:** Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.
