
# Day 02 | Identity Inventory Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | SOC-AD-D02-001 |
| Exercise | Active Directory Identity Inventory |
| Date | 2026-11-04 |
| Environment | Proxmox VE, Windows Server 2022 |
| Domain Controller | DC01 |
| Domain | corp.local |
| Account Under Review | Daniel Carter (`dcarter`) |
| Activity Type | Identity baseline review |
| Status | Completed |
| Disposition | Baseline documented. No security incident established. |

## 1. Initial Objective

Establish an evidence-based identity baseline for an existing Active Directory account before performing subsequent identity administration or security investigations.

The review focused on:

- Account identity and logon names
- Account configuration and expiration settings
- Direct security group membership
- Organizational attributes
- Directory creation and modification timestamps
- Recorded authentication attributes
- Account enabled status

This was a planned identity inventory exercise, not an investigation triggered by a security alert.

The objective was to document the account's observed configuration and identify limitations requiring further verification.

## 2. Systems and Evidence Sources

- DC01 running Windows Server 2022
- Active Directory domain: corp.local
- Active Directory Users and Computers (ADUC)
- Account properties and Account tab
- Member Of tab
- Organization tab
- Attribute Editor
- Administrative Windows PowerShell

The account under review was Daniel Carter (`dcarter`), located in:

`corp.local > CorpOps > Employees`

ADUC was used to inspect account properties, membership, organizational information, and directory attributes.

PowerShell was used to verify the account's enabled status.

No Windows Security authentication events were retrieved during this exercise.

## 3. Actions and Verified Results

| Action | Verified Result |
| --- | --- |
| Locate `dcarter` in ADUC | Account present in CorpOps Employees OU |
| Inspect account identity | Daniel Carter |
| Review user principal name | `dcarter@corp.local` |
| Review legacy logon name | `CORP\dcarter` |
| Inspect account expiration | Never |
| Inspect password never expires | Unchecked |
| Inspect reversible password encryption option | Unchecked |
| Inspect mandatory password change at next logon | Unchecked |
| Inspect Member Of tab | Domain Users was the only displayed direct membership |
| Inspect Organization tab | Job title, department, company, and manager not populated |
| Review `whenCreated` | October 12, 2026, 4:24:52 AM |
| Review `whenChanged` | October 12, 2026, 4:33:17 AM |
| Review `lastLogon` | October 18, 2026, 3:57:57 AM |
| Review `lastLogonTimestamp` | October 12, 2026, 4:32:37 AM |
| Review `logonCount` | 7 |
| Review `badPwdCount` | 0 |
| Review `lockoutTime` | 0 |
| Verify enabled status using PowerShell | True |

The directory timestamps were displayed in Pacific Daylight Time.

These findings represent the account's observed state at the time of inspection.

The checks established an initial identity configuration baseline but did not constitute a complete authentication, authorization, or security audit.

## 4. Technical Evidence and Findings

### Finding 01: Account Identity and Location

**Verification method:** Active Directory Users and Computers.

**Observed:** The Daniel Carter account was present in the CorpOps Employees OU.

The account identifiers were:

- Displayed identity: Daniel Carter
- User principal name: `dcarter@corp.local`
- Legacy logon name: `CORP\dcarter`

**Interpretation:** The account exists in the directory and has identifiable logon attributes that can be used for subsequent identity administration and security investigations.

**Limitation:** Account presence and naming information do not independently establish ownership, authorization, or successful authentication.

### Finding 02: Account Configuration

**Verification method:** ADUC Account tab.

**Observed:**

- Account expiration was set to Never.
- Password never expires was unchecked.
- Reversible password encryption was unchecked.
- Mandatory password change at next logon was unchecked.

**Interpretation:** These observations establish the visible account configuration at the time of inspection.

An account configured without an expiration date is not automatically misconfigured. Its appropriateness depends on organizational policy and account purpose.

**Limitation:** The inspected settings do not independently establish password policy compliance, password quality, or the complete security state of the account.

### Finding 03: Direct Group Membership

**Verification method:** ADUC Member Of tab.

**Observed:** Domain Users was the only direct group membership displayed.

No direct privileged group membership was visible in the inspected view.

**Interpretation:** The observed membership provides an initial reference for future group membership and access reviews.

**Limitation:** The Member Of tab does not independently establish complete effective permissions, delegated access, or every possible authorization path.

No conclusion about the account's complete privilege level can be made from this view alone.

### Finding 04: Organizational Attributes

**Verification method:** ADUC Organization tab.

**Observed:** The following attributes were not populated:

- Job title
- Department
- Company
- Manager

**Interpretation:** These fields represent missing organizational information in the inspected directory record.

They may reduce the context available during identity investigations and access reviews.

**Limitation:** Missing organizational attributes do not independently establish unauthorized activity or a policy violation.

An authoritative identity standard or HR record would be needed to determine whether the missing values represent a compliance issue.

### Finding 05: Directory Creation and Modification Timestamps

**Verification method:** ADUC Attribute Editor.

**Observed:**

| Attribute | Recorded Value |
| --- | --- |
| `whenCreated` | October 12, 2026, 4:24:52 AM |
| `whenChanged` | October 12, 2026, 4:33:17 AM |

The values were displayed in Pacific Daylight Time.

**Interpretation:** The timestamps provide historical directory metadata for the account.

The `whenCreated` value records account object creation time, while `whenChanged` records the most recent modification time represented by that attribute in the inspected directory data.

**Limitation:** The modification timestamp does not identify which attribute changed, who performed the change, or whether the action was authorized.

Directory change auditing would be required for stronger attribution.

### Finding 06: Recorded Authentication Attributes

**Verification method:** ADUC Attribute Editor.

**Observed:**

| Attribute | Recorded Value |
| --- | --- |
| `lastLogon` | October 18, 2026, 3:57:57 AM |
| `lastLogonTimestamp` | October 12, 2026, 4:32:37 AM |
| `logonCount` | 7 |
| `badPwdCount` | 0 |
| `lockoutTime` | 0 |

The recorded `lastLogon` and `lastLogonTimestamp` values differed.

**Interpretation:** These attributes provide historical authentication-related information associated with the directory account.

The `lastLogon` and `lastLogonTimestamp` attributes have different update and replication behaviors.

Their differing values do not independently establish suspicious activity.

**Limitation:** These attributes are not a complete authentication event history.

The observed `logonCount` value must not be presented as proof of seven successful logon events during a particular investigation period.

The observed `badPwdCount` value does not prove that no failed authentication attempts occurred historically.

No Windows Security authentication events were retrieved to establish individual logon times, source workstations, logon types, or authorization context.

### Finding 07: Account Enabled Status

**Verification method:** Administrative PowerShell.

**Command executed:**

```powershell
Get-ADUser -Identity dcarter -Properties Enabled | Select-Object Name,Enabled
```

**Observed output:**

```text
Name           Enabled
----           -------
Daniel Carter  True
```

**Observed:** The account returned `Enabled = True`.

**Interpretation:** The account was enabled at the time of the PowerShell verification.

**Limitation:** An enabled account is not proof of successful authentication, appropriate permissions, or authorized account use.

The check established the account's reported enabled state only.

## 5. Analyst Assessment

### Observed

- The Daniel Carter account existed in the CorpOps Employees OU.
- The account used the identifiers `dcarter@corp.local` and `CORP\dcarter`.
- Account expiration was set to Never.
- The inspected password-related options were unchecked.
- Domain Users was the only displayed direct group membership.
- Organizational attributes were not populated.
- Directory creation and modification timestamps were recorded.
- Authentication-related attributes contained historical values.
- `lastLogon` and `lastLogonTimestamp` contained different timestamps.
- `logonCount` was 7.
- `badPwdCount` was 0.
- `lockoutTime` was 0.
- PowerShell confirmed `Enabled = True`.

These observations document the inspected account state during the planned baseline review.

### Correlated

ADUC and PowerShell provided complementary observations of the same Active Directory identity.

The account properties established identity and configuration details.

The Member Of tab provided the displayed direct group membership.

The Organization tab identified unpopulated organizational fields.

The Attribute Editor exposed directory metadata and authentication-related attributes.

PowerShell independently queried the account's enabled status.

Together, these findings support the establishment of an initial account inventory.

The recorded `lastLogon` and `lastLogonTimestamp` values differed, consistent with the attributes' different update and replication behaviors.

No event-level authentication correlation was performed.

### Interpretation

The collected information establishes an initial identity baseline for Daniel Carter.

The account exists, was enabled, and had Domain Users as its only displayed direct membership.

The unpopulated organizational attributes represent a documentation gap requiring comparison against an approved identity standard.

The historical authentication-related values provide context but do not establish whether individual authentication attempts were authorized.

A directory modification timestamp cannot independently identify the actor or the specific change.

No unauthorized activity or security incident was established during this exercise.

### Unknown

The following areas were not fully validated during Day 02:

- Account ownership against an authoritative HR record
- Approved department and manager information
- Complete effective permissions and delegated access
- Authentication source workstation and logon type
- Windows Security Event ID 4624 and 4625 records
- Directory modification audit events
- Authorization for historical account changes
- Complete authentication history across relevant domain controllers

These areas require separate verification before stronger security conclusions can be made.

## 6. Actions and Changes

This was a read-only identity baseline exercise.

The activities consisted of:

- Locating the existing account in ADUC
- Reviewing account configuration
- Inspecting direct group membership
- Inspecting organizational attributes
- Reviewing directory timestamps
- Reviewing authentication-related directory attributes
- Verifying enabled status using PowerShell
- Documenting the observed results

No account configuration changes were intentionally performed.

No password reset, account unlock, group membership change, or account modification was performed as part of the documented exercise.

No remediation or detection tuning was performed.

### Analyst Reasoning Exercise

A hypothetical scenario involving seven successful logons and no failed logons was used to practice investigative reasoning.

This scenario was not derived from retrieved Windows Security events.

The exercise reinforced that normal-looking authentication activity requires supporting context before it can be classified as authorized.

The hypothetical scenario is not evidence of actual authentication activity.

## 7. Classification and Disposition

**Classification:** Planned Active Directory identity baseline review.

**Disposition:** Identity baseline completed and documented.

**Security Conclusion:** No security incident was established during the review.

The available observations did not demonstrate unauthorized activity.

Missing organizational information was documented as an identity record completeness gap rather than a confirmed security policy violation.

No corrective action was required based solely on the observed findings.

The absence of an identified incident during the baseline review does not establish that all historical account activity was authorized.

## 8. Verification and Evidence Gaps

The completed ADUC inspections and PowerShell verification support the documented account configuration and enabled status.

However, the following were not comprehensively validated:

- Authoritative account ownership
- Organizational attribute accuracy
- Effective access permissions
- Privileged access through indirect authorization paths
- Authentication source systems
- Authentication logon types
- Windows Security Event ID 4624 and 4625 records
- Directory modification audit records
- Change approval and authorization history

These items remain outside the completed Day 02 scope.

The recorded authentication-related directory attributes must not be treated as substitutes for a complete authentication event investigation.

No unsupported conclusions about account compromise, misuse, or policy violations should be inferred from the available observations.

## 9. Handover Notes

An analyst continuing this work should use the documented identity baseline when reviewing future account changes or investigating suspicious authentication activity.

### Baseline Reference

| Item | Documented State |
| --- | --- |
| Domain controller | DC01 |
| Domain | corp.local |
| Account | Daniel Carter (`dcarter`) |
| Account location | CorpOps Employees OU |
| User principal name | `dcarter@corp.local` |
| Legacy logon name | `CORP\dcarter` |
| Enabled status | True |
| Account expiration | Never |
| Displayed direct membership | Domain Users |
| Organizational attributes | Not populated |
| `logonCount` | 7 |
| `badPwdCount` | 0 |
| `lockoutTime` | 0 |
| Investigation disposition | Baseline documented |

### Investigation Considerations

When investigating subsequent account activity, compare the current directory state against the Day 02 observations.

Determine:

- Whether the account remains enabled.
- Whether account configuration has changed.
- Whether group memberships have changed.
- Whether organizational information has been updated.
- Whether directory modification activity can be attributed to an authorized actor.
- Whether authentication events support the activity under investigation.

Do not classify account activity as suspicious solely because a directory timestamp changed or because authentication-related attributes contain historical values.

### Outstanding Verification

Account ownership, effective permissions, authentication sources, event-level logon history, and directory change authorization were not comprehensively validated.

Previously completed Day 02 checks should not be repeated unless a new investigation, account change, access review, or troubleshooting requirement makes revalidation necessary.

## 10. Next Action

Proceed to Day 03 under the approved 90 Days of SOC Tier 1 and Tier 2 Mastery roadmap.

Use the documented Day 02 identity baseline as a reference for subsequent account administration and security investigations.

Any additional checks must follow the approved Day 03 scope and must not be represented as completed Day 02 work.

## Documentation Principle

Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.

