
# Day 02 | Identity Inventory Investigation Notes

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Case ID:** SOC-AD-D02-001  
**Investigation Date:** Nov 04, 2026  
**System:** DC01, Windows Server 2022  
**Domain:** `corp.local`  
**Account:** Daniel Carter (`dcarter`)  
**Investigation Type:** Identity baseline review  
**Status:** Completed

## 1. Initial Objective

Establish an evidence-based identity baseline for an existing Active Directory account.

The review focused on account settings, direct group membership, organizational attributes, directory timestamps, recorded authentication attributes, and enabled status.

No security alert triggered this investigation.

## 2. Initial Signal

The account `dcarter` was identified in:

`corp.local > CorpOps > Employees`

The account was selected for a planned baseline review.

## 3. Evidence Sources

| Source | Information Reviewed |
|---|---|
| Active Directory Users and Computers | Account properties |
| Account tab | Logon name and account settings |
| Member Of tab | Direct group membership |
| Organization tab | Organizational information |
| Attribute Editor | Directory metadata and logon attributes |
| Windows PowerShell | Enabled status |

## 4. Observed Findings

### Identity

The account was identified as Daniel Carter.

The user principal name was `dcarter@corp.local`, and the legacy logon name was `CORP\dcarter`.

### Account Configuration

Account expiration was set to Never.

The visible options for password never expires, reversible password encryption, and mandatory password change at next logon were unchecked.

### Group Membership

The Member Of tab displayed Domain Users as the only direct group membership.

No direct privileged group membership was visible.

### Organizational Information

Job title, department, company, and manager were not populated.

### Directory Timestamps

| Attribute | Recorded Value |
|---|---|
| `whenCreated` | October 12, 2026, 4:24:52 AM |
| `whenChanged` | October 12, 2026, 4:33:17 AM |

Times were displayed in Pacific Daylight Time.

### Authentication Attributes

| Attribute | Recorded Value |
|---|---|
| `lastLogon` | October 18, 2026, 3:57:57 AM |
| `lastLogonTimestamp` | October 12, 2026, 4:32:37 AM |
| `logonCount` | 7 |
| `badPwdCount` | 0 |
| `lockoutTime` | 0 |

### Enabled Status

PowerShell command:

```powershell id="d3x9qv"
Get-ADUser -Identity dcarter -Properties Enabled | Select-Object Name,Enabled
```

Observed result:

```text id="v6k2rp"
Name           Enabled
----           -------
Daniel Carter  True
```

The account was enabled at the time of verification.

## 5. Correlation

The identity was confirmed across the account properties, group membership view, Attribute Editor, and PowerShell output.

The recorded `lastLogon` and `lastLogonTimestamp` values differed.

This difference was interpreted in the context of the attributes' different update and replication behaviors.

No Windows Security authentication events were retrieved during this exercise.

## 6. Interpretation

The available evidence supports the following conclusions:

1. The account exists and was enabled.
2. Domain Users was the only direct group membership displayed.
3. Organizational attributes were incomplete.
4. Directory creation and modification timestamps were recorded.
5. Authentication-related attributes contained historical values.
6. No unauthorized activity was established.

The directory modification timestamp did not identify who changed the account or which attributes changed.

The authentication attributes did not establish whether individual logons were authorized.

## 7. Evidence Gaps

The following items were not verified:

1. Account ownership against an authoritative HR record.
2. Approved department and manager information.
3. Complete effective permissions and delegated access.
4. Authentication source workstation and logon type.
5. Windows Security Event ID 4624 and 4625 records.
6. Directory modification audit events and change authorization.

These limitations prevent a stronger security conclusion.

## 8. Analyst Reasoning Exercise

A hypothetical scenario involving seven successful logons and no failed logons was used to practice investigative reasoning.

The scenario was not based on retrieved Windows Security events.

The exercise reinforced that normal-looking authentication activity requires appropriate context before being classified as authorized.

## 9. Classification and Disposition

**Classification:** Planned identity baseline review  
**Security Incident:** Not established  
**Disposition:** Baseline documented  
**Account Changes:** None intentionally performed

The available evidence did not demonstrate suspicious activity.

Missing organizational information was recorded as a documentation gap rather than a confirmed policy violation.

## 10. Verification

The final PowerShell check returned:

`Enabled = True`

The account remained enabled at the time of verification.

No corrective action was required based solely on the observed findings.

## 11. Outstanding Items

Compare the account's organizational information against an approved identity standard when available.

Use authentication and directory auditing evidence in a future investigation if suspicious activity is identified.

## 12. Handover Notes

An analyst continuing this work should begin with the documented account baseline and supporting screenshots in the `evidence` directory.

The account was enabled, had Domain Users as its only displayed direct membership, and lacked populated organizational details.

No security incident was confirmed.

Further investigation should be driven by a new alert, an access review requirement, or additional evidence rather than assuming the existing attributes indicate malicious activity.
