
# Day 07 | Investigation Notes

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Phase:** 01 Active Directory & Help Desk  
**Exercise:** Create OUs & Security Groups  
**Status:** Hands-on complete  
**Domain:** `corp.local`  
**Domain Controller:** `DC01`  
**Environment:** Windows Server 2022, Proxmox VE  
**Investigation Tool:** Active Directory Users and Computers (ADUC)

## 1. Investigation Objective

Extend the existing Active Directory identity structure by creating departmental Organizational Units and Global Security groups.

Verify that the objects were created in their intended locations and configured with the correct object types.

Document an object creation mistake involving `GG_HR_Users`, the observed correction, and the limitations of the available evidence.

This was an authorized administrative lab exercise, not an investigation of a detected security incident.

## 2. Investigation Scope

The exercise covered:

- Reviewing the existing IT security groups.
- Creating Finance, HumanResources, and Operations OUs.
- Verifying the departmental OU hierarchy.
- Creating three departmental Global Security groups.
- Identifying an incorrectly created HR Organizational Unit.
- Correcting the HR object configuration.
- Inspecting the final IT group inventory.
- Verifying direct group membership lists.
- Reviewing the distinction between directory organization and effective permissions.

No resource permissions, administrative delegation, or Group Policy settings were configured during this exercise.

## 3. Environment Baseline

The existing domain was `corp.local`, managed through DC01.

Before Day 07, the organizational structure included:

```text
corp.local
└── CorpOps
    ├── Employees
    ├── IT
    ├── ServiceAccounts
    └── Workstations
```

Four Global Security groups already existed in the IT OU from Day 06:

- `GG_HelpDesk_Tier1`
- `GG_HelpDesk_Tier2`
- `GG_SOC_Analysts`
- `GG_IT_Admins`

These groups were baseline objects and were not recreated during Day 07.

The intended changes were three departmental OUs beneath Employees and three departmental security groups beneath IT.

## 4. Investigation Questions and Initial Hypotheses

### Question 1

Were the departmental OUs created in the correct location?

**Initial hypothesis:** The Finance, HumanResources, and Operations OUs should appear beneath `CorpOps/Employees`.

### Question 2

Were the departmental security groups created with the correct object type?

**Initial hypothesis:** Each intended departmental group should be a Group object configured with Global scope and Security type.

### Question 3

Does an object name establish its actual directory object class?

**Initial hypothesis:** Naming conventions describe intent, but the actual object class must be verified.

### Question 4

Does creating a security group automatically provide resource access?

**Initial hypothesis:** Group creation does not independently establish membership or resource permissions.

## 5. Investigation Timeline

Exact timestamps were not established in the available Day 07 evidence.

The following sequence represents the documented order of activities rather than a reconstructed event timeline.

| Step | Action | Observed Result |
| --- | --- | --- |
| 01 | Inspected existing IT groups | Four Day 06 groups present |
| 02 | Created Finance OU | OU creation dialog showed accidental deletion protection selected |
| 03 | Created HumanResources OU | OU creation dialog showed accidental deletion protection selected |
| 04 | Created Operations OU | OU creation dialog showed accidental deletion protection selected |
| 05 | Inspected Employees hierarchy | Three departmental OUs visible |
| 06 | Created Finance security group | Global Security group configuration shown |
| 07 | Attempted to create HR security group | Organizational Unit selected by mistake |
| 08 | Inspected incorrect HR object | `GG_HR_Users` appeared as an OU |
| 09 | Returned to Group creation workflow | Correct Global Security group configuration shown |
| 10 | Inspected corrected HR directory state | HR group visible; incorrect OU no longer visible |
| 11 | Created Operations security group | Global Security group configuration shown |
| 12 | Inspected final IT inventory | Seven security groups with descriptions |
| 13 | Inspected departmental group memberships | Empty direct membership lists |

## 6. Actions Performed and Observed Evidence

### 6.1 Existing IT Group Baseline

**Action:** Opened the IT OU in ADUC and inspected its existing security groups.

**Observation:**

Four groups from Day 06 were present:

- `GG_HelpDesk_Tier1`
- `GG_HelpDesk_Tier2`
- `GG_SOC_Analysts`
- `GG_IT_Admins`

**Interpretation:**

The baseline established which objects existed before the Day 07 changes.

This prevented the four existing groups from being incorrectly attributed to the current exercise.

### 6.2 Departmental OU Creation

**Action:** Used ADUC to create three Organizational Units under:

`corp.local/CorpOps/Employees`

**Observed objects:**

- Finance
- HumanResources
- Operations

The creation dialogs showed accidental deletion protection selected.

The subsequent directory inspection displayed all three OUs beneath Employees.

**Interpretation:**

The intended departmental directory structure was established.

The observed OU placement did not demonstrate that Group Policy, delegated administration, or resource permissions had been configured.

### 6.3 Finance Security Group Creation

**Action:** Created `GG_Finance_Users` under:

`corp.local/CorpOps/IT`

**Observation:**

- Group scope: Global.
- Group type: Security.
- The group appeared in the IT OU after creation.

**Interpretation:**

The Finance group was established as a directory object for future role-based access design.

No resource access was demonstrated.

### 6.4 Incorrect Human Resources Object

**Intended action:** Create a Global Security group named `GG_HR_Users`.

**Actual action:** Selected Organizational Unit instead of Group.

**Observation:**

An OU named `GG_HR_Users` appeared beneath the IT OU.

The object used a name consistent with the intended group naming convention but had the wrong object type.

**Interpretation:**

The naming convention did not determine the object's actual class.

This created a configuration discrepancy between the intended identity design and the observed directory state.

### 6.5 Human Resources Configuration Correction

**Action:** Returned to the Group creation workflow and configured:

- Name: `GG_HR_Users`
- Scope: Global
- Type: Security
- Location: `corp.local/CorpOps/IT`

**Observation:**

The later IT OU inventory showed `GG_HR_Users` as a security group.

The incorrectly created OU was no longer visible beneath IT.

**Evidence boundary:**

The screenshots establish the incorrect OU, the correct group creation configuration, and the later directory state.

They do not independently establish every intermediate cleanup action or the exact commands used.

No undocumented deletion procedure is assumed.

### 6.6 Operations Security Group Creation

**Action:** Created `GG_Operations_Users` under the IT OU.

**Observation:**

The creation dialog showed:

- Group scope: Global.
- Group type: Security.

**Interpretation:**

The Operations group was established using the intended configuration.

### 6.7 Final Group Inventory

**Action:** Inspected the IT OU after completing the departmental groups.

**Observation:**

Seven security groups were visible.

| Existing from Day 06 | Added during Day 07 |
| --- | --- |
| `GG_HelpDesk_Tier1` | `GG_Finance_Users` |
| `GG_HelpDesk_Tier2` | `GG_HR_Users` |
| `GG_SOC_Analysts` | `GG_Operations_Users` |
| `GG_IT_Admins` | |

The three new departmental groups had populated descriptions.

**Interpretation:**

The inventory established the final observed group structure.

Descriptions documented intended responsibilities but did not enforce membership or permissions.

### 6.8 Group Membership Inspection

**Action:** Inspected the Members tabs for the three departmental groups.

**Observation:**

- `GG_Finance_Users`: Empty direct membership list.
- `GG_HR_Users`: Empty direct membership list.
- `GG_Operations_Users`: Empty direct membership list.

**Interpretation:**

No users or nested groups were observed as direct members at inspection time.

The results did not establish whether permissions had been assigned elsewhere.

## 7. Correlation and Analyst Reasoning

### Observation

Three departmental OUs and three departmental Global Security groups were created.

An incorrectly typed HR object was identified before the correct HR group appeared in the final directory inventory.

### Interpretation

The directory structure matched the intended departmental organization in the final inspected state.

However, object names alone were insufficient to establish object type or effective access.

### Correlation

The initial HR creation dialog and incorrect OU hierarchy showed the configuration mistake.

The corrected Group creation dialog and later IT inventory showed the intended HR group configuration.

Together, these observations supported the conclusion that the final inspected directory state differed from the incorrect intermediate state.

### Initial Disposition

Expected authorized Active Directory administration activity with an identified configuration mistake and subsequent correction.

### Why

The changes occurred within the controlled Day 07 lab exercise.

No evidence of unauthorized directory changes, privilege misuse, or resource access was collected.

**Final security classification:** No security incident identified or confirmed.

## 8. Detection and Investigation Considerations

In a production SOC investigation, unexpected OU or security group creation would require additional evidence.

Relevant investigation questions include:

1. Which account created the object?
2. Was the creation authorized?
3. What was the object's actual directory class?
4. Was the object created in the expected OU?
5. Were any users or groups added?
6. Were resource permissions or delegated rights assigned?
7. Did the changes result in suspicious account activity?

Relevant Windows Security events for future investigation include:

| Event ID | Investigation Use |
| --- | --- |
| 5137 | Directory service object created, when appropriate auditing is enabled |
| 5136 | Directory service object modified, when appropriate auditing is enabled |
| 5141 | Directory service object deleted, when appropriate auditing is enabled |
| 4727 | Security-enabled global group created |
| 4728 | Member added to a security-enabled global group |
| 4737 | Security-enabled global group changed |

These event IDs are reference points for future investigations.

**No Day 07 Windows Security event collection or validation was demonstrated.**

No detection rule was created or tested.

## 9. Access Design Considerations

The exercise introduced departmental Global Security groups that could support the AGDLP model:

```text
Accounts
   |
   v
Global Groups
   |
   v
Domain Local Groups
   |
   v
Permissions
```

The following were not implemented:

- Domain Local resource permission groups.
- NTFS permissions.
- Shared folder permissions.
- Delegated administrative rights.
- Effective access tests.

The departmental OUs also did not have Group Policy configurations validated during Day 07.

The exercise established identity organization rather than effective authorization.

## 10. Evidence Gaps and Limitations

The available evidence did not establish:

- Exact timestamps for the administrative actions.
- The complete sequence used to remove the incorrect HR OU.
- Security event records proving each object creation or modification.
- The administrator identity responsible for each individual change.
- Historical group membership changes.
- Assigned resource permissions.
- Effective access to departmental resources.
- Group Policy application to the new OUs.
- A completed AGDLP permission chain.

ADUC screenshots demonstrate the inspected configuration states.

They do not provide a complete historical audit trail.

No missing evidence has been reconstructed or fabricated.

## 11. Final Assessment

**Classification:** Authorized administrative lab activity.

**Outcome:** Departmental OU and security group structure established.

**Confirmed findings:**

- Three departmental OUs created under Employees.
- Three Global Security groups created under IT.
- An incorrect HR OU identified.
- Correct HR group configuration observed.
- Final IT OU inventory contained seven security groups.
- Three departmental groups had empty direct membership lists.

**Not demonstrated:**

- Resource permission assignment.
- Effective access.
- Group Policy application.
- Unauthorized directory changes.
- Security incident activity.

**Final disposition:** Departmental identity structure established, with the HR object type mistake corrected in the final observed configuration.

No security incident was identified or confirmed.

## 12. Analyst Handoff

Day 07 is complete.

The following departmental OUs are existing lab objects:

- Finance
- HumanResources
- Operations

The following departmental security groups are also existing lab objects:

- `GG_Finance_Users`
- `GG_HR_Users`
- `GG_Operations_Users`

These objects should not be recreated during subsequent exercises.

Day 08 can build on this structure by creating the planned enterprise user accounts and verifying their departmental placement.

Any later account memberships, permissions, or authentication activity must be documented under the exercise in which they were actually verified.

No additional Day 07 hands-on activity is required.
