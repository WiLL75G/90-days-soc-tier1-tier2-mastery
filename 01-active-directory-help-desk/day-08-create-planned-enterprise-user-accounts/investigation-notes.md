
# Day 08 | Investigation Notes

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Phase:** 01 Active Directory & Help Desk  
**Exercise:** Create Planned Enterprise User Accounts  
**Status:** Hands-on complete  
**Domain:** `corp.local`  
**Investigation type:** Identity provisioning, group membership verification, and first-logon authentication

## 1. Investigation Objective

Verify that three planned enterprise employee accounts were correctly provisioned in Active Directory.

The investigation focused on five questions:

1. Were the accounts created in the intended departmental Organizational Units?
2. Were the correct departmental security groups assigned?
3. Could the accounts complete their mandatory first-logon password changes?
4. Could the employees successfully sign in to the domain-joined Windows 11 client?
5. Were there configuration discrepancies requiring additional investigation?

This was an authorized home-lab exercise, not a response to a production security incident.

## 2. Environment and Scope

| Component | Details |
| --- | --- |
| Hypervisor | Proxmox VE |
| Domain controller | DC01 |
| Server OS | Windows Server 2022 |
| Domain | `corp.local` |
| Endpoint | Domain-joined Windows 11 |
| Administrative interface | Active Directory Users and Computers |
| Departmental OUs | Finance, HumanResources, Operations |
| Departmental groups | `GG_Finance_Users`, `GG_HR_Users`, `GG_Operations_Users` |

The departmental OUs and Global Security groups were created during Day 07.

Day 08 reused those existing objects.

### Accounts in Scope

| Employee | Username | Department | Intended Group |
| --- | --- | --- | --- |
| Sarah Mitchell | `sarah.mitchell` | Finance | `GG_Finance_Users` |
| John Smith | `jsmith` | HumanResources | `GG_HR_Users` |
| Michael Johnson | `mjohnson` | Operations | `GG_Operations_Users` |

No production accounts, external identities, or customer environments were involved.

## 3. Initial Baseline

Before provisioning the employees, the existing Active Directory structure contained:

- `CorpOps/Employees/Finance`
- `CorpOps/Employees/HumanResources`
- `CorpOps/Employees/Operations`
- `CorpOps/IT`

The corresponding departmental Global Security groups were located under the IT OU.

These objects provided the organizational structure for the planned employee accounts.

The baseline did not independently establish any departmental resource permissions.

## 4. Investigation Hypotheses

### Hypothesis 1: Account Provisioning

If the accounts were provisioned correctly, each employee should appear in the intended departmental OU with the expected logon name.

### Hypothesis 2: Departmental Group Membership

If the membership configuration was correct, the intended group should be visible in the employee's Member Of tab, and the employee should appear in the group's Members tab.

### Hypothesis 3: First-Logon Password Enforcement

If the password-change requirement was enabled and applied as expected, Windows should require the employee to change the initial password during the first sign-in.

### Hypothesis 4: Interactive Authentication

If the account could successfully authenticate and complete the first-logon workflow, Windows should permit the employee to reach an interactive desktop session.

These hypotheses guided the verification process. They were not assumptions of success.

## 5. Investigation Timeline

The following sequence reflects the documented order of activity.

Exact timestamps were not recorded in the investigation notes.

### Activity 01: Employee Account Provisioning

Created the following accounts through ADUC:

- Sarah Mitchell in Finance.
- John Smith in HumanResources.
- Michael Johnson in Operations.

Configured initial passwords and enabled the requirement to change the password at the next logon.

**Observation:** The three employee accounts appeared in their intended departmental OUs.

**Interpretation:** The planned identity objects had been provisioned in the expected organizational locations.

**Limitation:** Account existence alone did not establish successful authentication.

### Activity 02: Finance Membership Verification

Inspected Sarah Mitchell's account properties and the Finance group's membership.

**Observation:** `GG_Finance_Users` was listed for Sarah Mitchell, and the group's Members tab displayed her account.

**Interpretation:** The inspected Active Directory views agreed with the intended Finance membership.

**Disposition:** Expected membership observed.

### Activity 03: HumanResources Membership Verification

Inspected John Smith's account properties.

Reopened the properties to confirm the membership remained visible.

**Observation:** `GG_HR_Users` was listed under the account's group memberships.

**Interpretation:** The observed account configuration matched the intended HumanResources assignment.

**Disposition:** Expected membership observed.

### Activity 04: Operations Membership Discrepancy

Inspected Michael Johnson's Member Of tab.

**Observation:** `GG_Operations_Users` was listed.

Opened the Operations group's Members tab.

**Observation:** The Members list appeared empty.

Reopened the account and group properties.

**Observation:** The same disagreement remained visible.

**Interpretation:** Two inspected administrative views did not agree about the intended membership.

**Initial hypothesis:** The membership might not have been saved consistently.

**Important limitation:** The underlying cause had not been established.

The discrepancy was not sufficient evidence of malicious activity, unauthorized modification, or an Active Directory synchronization failure.

### Activity 05: Operations Membership Correction

Used the Operations group's Add Members dialog to resolve and add Michael Johnson.

Saved the configuration.

Closed and reopened the group properties.

**Observation:** Michael Johnson appeared in the group's Members tab.

Inspected the account properties again.

**Observation:** `GG_Operations_Users` remained visible in the Member Of tab.

**Interpretation:** The final inspected views agreed with the intended membership.

**Disposition:** Configuration discrepancy resolved.

**Root cause:** Undetermined.

### Activity 06: Sarah Mitchell First Sign-In

Attempted the first interactive domain sign-in using Sarah Mitchell's account.

Windows displayed:

> The user's password must be changed before signing in.

Completed the password-change workflow.

Windows confirmed:

> Your password has been changed.

The Windows desktop loaded.

**Interpretation:** The observed first-logon workflow matched the configured password-change requirement.

**Disposition:** Successful first-logon test.

### Activity 07: John Smith First Sign-In

Attempted the first interactive domain sign-in using John Smith's account.

**Observations:**

- Windows required a password change.
- The password change completed successfully.
- Windows displayed a successful password-change confirmation.
- The desktop loaded.
- The Start menu displayed John Smith.

**Interpretation:** The expected first-logon workflow completed.

**Disposition:** Successful first-logon test.

### Activity 08: Michael Johnson First Sign-In

Attempted the first interactive domain sign-in using Michael Johnson's account.

**Observations:**

- Windows required a password change.
- The password change completed successfully.
- The desktop loaded.
- The Start menu displayed Michael Johnson.

**Interpretation:** The account successfully completed the interactive sign-in workflow following the membership correction.

**Disposition:** Successful first-logon test.

This result did not independently prove access to Operations resources.

## 6. Key Finding: Conflicting Membership Views

### Finding ID

`DAY08-F01`

### Finding

Operations group membership discrepancy during account provisioning.

### Affected Identity

`corp.local` — Michael Johnson (`mjohnson`)

### Expected State

Michael Johnson should belong to `GG_Operations_Users`.

### Initial Observed State

The account's Member Of tab displayed the intended group.

The group's Members tab appeared empty.

### Verification Action

Reopened the account and group properties and inspected the membership again.

The discrepancy remained visible.

### Corrective Action

Used the group's Add Members dialog to add Michael Johnson.

### Final Observed State

Michael Johnson appeared in the group's Members tab, and the intended group remained listed in his account properties.

### Root Cause Assessment

Not determined.

The available evidence does not establish whether the initial discrepancy resulted from an incomplete membership operation, an administrative display issue, or another cause.

No definitive root cause should be assigned without additional evidence.

### Security Assessment

No malicious activity was established.

This was an identity configuration discrepancy discovered during an authorized provisioning exercise.

## 7. Observation, Interpretation, Correlation, Disposition, Why

### Observation

Three departmental accounts were provisioned.

Their intended security group memberships were verified.

One Operations membership discrepancy required correction.

All three accounts completed mandatory first-logon password changes and reached Windows desktop sessions.

### Interpretation

The observed results were consistent with successful account provisioning and interactive domain authentication.

The Operations discrepancy showed that a configuration should not be treated as verified when inspected evidence conflicts.

### Correlation

Compared:

- ADUC departmental account placement.
- Account Member Of properties.
- Security group Members properties.
- Windows first-logon password-change prompts.
- Password-change confirmations.
- Observed desktop sessions.

This was manual correlation of configuration and endpoint behavior.

No Windows Security event correlation or SIEM investigation was performed during Day 08.

### Initial Disposition

Planned identity provisioning and first-logon verification completed successfully.

The Operations membership discrepancy was corrected.

No confirmed security incident.

### Why

The documented Active Directory inspections supported the final departmental configuration.

The Windows 11 tests supported successful first-logon authentication.

The evidence did not support a conclusion about effective resource authorization or the original cause of the Operations discrepancy.

## 8. SOC Detection and Investigation Relevance

The exercise provides context for future investigations involving Active Directory identity changes.

Relevant Windows Security event IDs to investigate in a later audit-enabled exercise include:

| Event ID | Investigation Relevance |
| --- | --- |
| 4720 | User account created |
| 4722 | User account enabled |
| 4723 | Attempt to change an account password |
| 4724 | Attempt to reset an account password |
| 4727 | Security-enabled global group created |
| 4728 | Member added to a security-enabled global group |
| 4737 | Security-enabled global group changed |
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4768 | Kerberos authentication ticket requested |
| 4771 | Kerberos pre-authentication failed |

These are **future investigation references**.

Their presence, absence, timestamps, or correlation were not verified during Day 08.

The account provisioning and authentication results must not be represented as SIEM detections.

## 9. Authentication Versus Authorization

Day 08 demonstrated successful interactive authentication.

It did not validate access to departmental resources.

For example, Michael Johnson reaching the Windows desktop did not prove that he could access an Operations shared folder.

Resource authorization would require additional checks such as:

- Effective NTFS permissions.
- Share permissions.
- Group-based resource assignments.
- Applicable access restrictions.
- Actual resource access tests.

These checks were outside Day 08's scope.

## 10. Evidence Register

The following filenames correspond to the eight selected references in the Day 08 README.

The original screenshots must be matched before uploading them under these filenames.

| Evidence | Filename | Intended Proof |
| --- | --- | --- |
| 01 | `day-08-01-departmental-user-inventory.png` | Employee account placement |
| 02 | `day-08-02-finance-group-membership.png` | Finance membership |
| 03 | `day-08-03-operations-membership-discrepancy.png` | Conflicting Operations membership views |
| 04 | `day-08-04-operations-membership-corrected.png` | Corrected Operations membership |
| 05 | `day-08-05-sarah-password-change.png` | Sarah's password-change confirmation |
| 06 | `day-08-06-john-successful-signin.png` | John's interactive session |
| 07 | `day-08-07-michael-successful-signin.png` | Michael's interactive session |
| 08 | `day-08-08-final-account-configuration.png` | Final AD account configuration |

Screenshots must remain genuine and technically unmodified.

## 11. Evidence Gaps and Limitations

**Verified through the documented lab activity:**

- Three departmental account creations.
- Intended departmental OU placement.
- Final group membership configuration.
- Operations membership correction.
- Mandatory first-logon password changes.
- Successful interactive Windows sign-ins.

**Not verified:**

- Root cause of the original Operations membership discrepancy.
- Domain controller Security event records for account creation.
- Security event records for group membership changes.
- Kerberos authentication event correlation.
- SIEM alert generation.
- NTFS or share permissions.
- Effective departmental resource access.
- Administrative delegation boundaries.

The exercise does not demonstrate a production SOC incident investigation or professional MSSP experience.

## 12. Final Investigation Disposition

**Classification:** Authorized identity provisioning and verification exercise.

**Result:** Completed.

**Accounts:** Three.

**Configuration discrepancies identified:** One.

**Configuration discrepancies corrected:** One.

**Confirmed malicious activity:** None.

**Root cause of discrepancy:** Undetermined.

**Authentication result:** Three successful first-logon workflows.

**Resource authorization:** Not tested.

The final evidence supports the conclusion that the planned employee identities were provisioned, their intended memberships were verified, and they successfully completed interactive Windows sign-in.

The investigation reinforces the importance of separating observed facts from hypotheses and distinguishing configuration, authentication, and authorization.

## 13. Analyst Handoff

Day 08 established three departmental employee accounts that can be used during later controlled identity and authentication exercises.

The next analyst should:

1. Preserve the original Day 08 screenshots.
2. Use the existing employee accounts without recreating them.
3. Treat the Operations discrepancy as resolved but without an established root cause.
4. Avoid claiming resource access or security event validation that was not performed.
5. Continue with the approved Day 09 authentication investigation.

**Next exercise:** Day 09 | Failed and Successful Logon Investigation.
