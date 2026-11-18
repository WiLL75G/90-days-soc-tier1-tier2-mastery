
# Day 06 | Investigation Notes

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Phase:** 01 Active Directory & Help Desk  
**Exercise:** Users, Security Groups & Access Design  
**Status:** Hands-on complete  
**Domain:** `corp.local`  
**Domain Controller:** `DC01`  
**Environment:** Windows Server 2022, Proxmox VE  
**Investigation Tool:** Active Directory Users and Computers (ADUC)

## 1. Investigation Objective

Examine existing Active Directory security groups, user memberships, and nested group relationships before creating role-specific security groups.

Determine what the observed directory configuration establishes about identity organization and what additional evidence would be required to verify effective access.

This was an authorized administrative lab exercise, not an investigation of a detected security incident.

## 2. Investigation Scope

The investigation covered:

- Existing Domain Admins and Domain Users groups.
- Daniel Carter's group membership.
- Built-in Users and Administrators group relationships.
- Existing Organizational Units.
- Creation of four role-specific Global Security groups.
- Group descriptions, scope, type, and membership state.
- AGDLP as an access design concept.

The exercise did not include resource permission assignments, administrative delegation, or effective access testing.

## 3. Environment Baseline

The existing domain was `corp.local`, managed through DC01.

The inspected organizational structure included:

```text
corp.local
├── Admins
└── CorpOps
    ├── Employees
    ├── IT
    ├── ServiceAccounts
    └── Workstations
```

Before creating the new groups, the IT, ServiceAccounts, and Admins OUs appeared empty in their inspected ADUC views.

Daniel Carter was an existing employee account under:

`corp.local/CorpOps/Employees`

No new employee accounts were created during Day 06.

## 4. Investigation Questions and Initial Hypotheses

### Question 1

Does a security group's name establish its administrative privileges?

**Initial hypothesis:** A group's name describes its intended purpose, but effective privileges depend on membership relationships and assigned permissions.

### Question 2

Does an employee's direct group membership provide a complete picture of authorization?

**Initial hypothesis:** Direct membership may not reveal relevant nested group relationships.

### Question 3

Does creating a Global Security group automatically assign access?

**Initial hypothesis:** Group creation establishes a directory object but does not independently configure resource permissions.

These questions guided the ADUC inspection and group creation exercise.

## 5. Investigation Timeline

Exact activity timestamps were not established in the available Day 06 evidence.

The sequence below records the documented order of actions, not a reconstructed timestamped event history.

| Step | Action | Observed Result |
| --- | --- | --- |
| 01 | Inspected Domain Admins | Global Security group with Administrator as a direct member |
| 02 | Inspected Domain Users | Global Security group |
| 03 | Inspected Daniel Carter | Domain Users displayed as primary group |
| 04 | Inspected built-in Users | Domain Users, Authenticated Users, and INTERACTIVE listed |
| 05 | Inspected Domain Admins membership relationships | Built-in Administrators relationship observed |
| 06 | Reviewed existing OUs | IT, ServiceAccounts, and Admins appeared empty |
| 07 | Created four IT role groups | Global Security groups created under CorpOps/IT |
| 08 | Added group descriptions | Intended responsibilities documented |
| 09 | Inspected new group memberships | Empty direct membership lists observed |
| 10 | Reviewed final IT OU inventory | All four groups visible with descriptions |

## 6. Actions Performed and Observed Evidence

### 6.1 Existing Privileged Group Inspection

**Action:** Opened Domain Admins properties in ADUC and inspected its configuration and Members tab.

**Observation:**

- Group scope: Global.
- Group type: Security.
- Administrator appeared as a direct member.

**Interpretation:**

The inspection established a direct membership relationship between the built-in Administrator account and Domain Admins at that time.

It did not establish when the membership was created or whether any privileged activity had occurred.

### 6.2 Domain Users Inspection

**Action:** Opened Domain Users properties.

**Observation:**

- Group scope: Global.
- Group type: Security.

**Interpretation:**

Domain Users and Domain Admins shared the same group scope and type.

Their different authorization roles could not be explained by scope and type alone.

### 6.3 Employee Membership Inspection

**Action:** Opened Daniel Carter's account properties and inspected the Member Of tab.

**Observation:**

- Domain Users was displayed.
- Domain Users was identified as the primary group.
- No additional direct memberships appeared in the captured view.

**Interpretation:**

The observed account membership established a relationship with Domain Users.

It did not demonstrate access to any specific resource.

### 6.4 Nested Group Inspection

**Action:** Inspected the built-in Users group.

**Observation:**

The Members tab displayed:

- Domain Users.
- Authenticated Users.
- INTERACTIVE.

The Domain Admins Member Of tab also identified the built-in Administrators group.

**Correlation:**

The observed relationships included:

```text
Daniel Carter
    |
    v
Domain Users
    |
    v
Builtin Users
```

**Interpretation:**

An analyst investigating access should consider relevant indirect group relationships, not only the memberships visible directly on a user account.

The observed chain did not establish effective access to a particular resource.

### 6.5 Organizational Unit Baseline

**Action:** Reviewed the existing Active Directory OUs.

**Observation:**

The inspected IT, ServiceAccounts, and Admins OUs appeared empty.

**Interpretation:**

The empty IT OU provided a baseline for identifying the groups created during this exercise.

### 6.6 Role Group Creation

**Action:** Created four groups under:

`corp.local/CorpOps/IT`

| Group | Scope | Type |
| --- | --- | --- |
| `GG_HelpDesk_Tier1` | Global | Security |
| `GG_HelpDesk_Tier2` | Global | Security |
| `GG_SOC_Analysts` | Global | Security |
| `GG_IT_Admins` | Global | Security |

**Observation:**

All four groups appeared in the IT OU.

Descriptions were added to identify their intended responsibilities.

**Interpretation:**

The groups established an organizational structure for future role assignments.

Their creation did not automatically grant privileges.

### 6.7 Membership Verification

**Action:** Inspected the Members tabs of the newly created groups.

**Observation:**

All four groups showed empty direct membership lists.

**Interpretation:**

No users or nested groups were observed as direct members at inspection time.

This did not establish whether permissions had been assigned elsewhere.

### 6.8 Object Properties Inspection

**Action:** Inspected GG_IT_Admins object properties.

**Observation:**

- Canonical path: `corp.local/CorpOps/IT/GG_IT_Admins`
- Object class: Group.
- Accidental deletion protection: Unchecked.

**Interpretation:**

The properties established the observed directory location and configuration of the object.

No deletion or modification resulting from the protection setting was demonstrated.

## 7. Correlation and Analyst Reasoning

### Observation

Existing group memberships and nested relationships were visible in ADUC.

Four new Global Security groups were created with empty direct membership lists.

### Interpretation

The new groups represented intended organizational roles, not demonstrated access privileges.

### Correlation

The existing Domain Admins and built-in Administrators relationship illustrated how group relationships may affect authorization.

The newly created GG_IT_Admins group had no observed direct members, and no relationship to Domain Admins was established.

### Initial Disposition

Expected authorized Active Directory administration activity.

No security incident identified or confirmed.

### Why

The observed activity was part of the controlled Day 06 lab exercise.

No evidence of unauthorized membership changes, resource access, or privilege misuse was collected.

## 8. Detection and Investigation Considerations

If similar group activity appeared in a SOC investigation, the following questions would require supporting evidence:

1. Which account created or modified the group?
2. Was the change approved?
3. Were users or other groups added?
4. Did the change affect privileged access?
5. What permissions were assigned?
6. Did the affected accounts subsequently exercise those permissions?

Relevant Windows Security events for future investigations may include:

| Event ID | Potential Investigation Use |
| --- | --- |
| 4727 | Security-enabled global group created |
| 4728 | Member added to a security-enabled global group |
| 4729 | Member removed from a security-enabled global group |
| 4737 | Security-enabled global group changed |
| 4755 | Security-enabled universal group changed |

These event IDs are investigation references only.

**No Day 06 security event collection or validation was demonstrated.**

No detection rule was created or tested during this exercise.

## 9. Access Design Considerations

The AGDLP model was reviewed:

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

The four newly created Global Security groups could support future role-based access design.

However, the following were not implemented:

- Domain Local resource permission groups.
- NTFS or shared folder access assignments.
- Delegated Active Directory administrative rights.
- Effective access verification.

The exercise established role group structure only.

## 10. Evidence Gaps and Limitations

The available evidence did not establish:

- Exact timestamps for the administrative actions.
- Security event records proving group creation.
- The administrator identity responsible for each change.
- Historical membership changes.
- Assigned resource permissions.
- Effective privileges of the new groups.
- Successful or denied access using group membership.
- A completed AGDLP authorization chain.

ADUC screenshots represent the observed directory state at inspection time.

They are not substitutes for historical audit records or effective access tests.

No missing evidence has been reconstructed or assumed.

## 11. Final Assessment

**Classification:** Authorized administrative lab activity.

**Outcome:** Four role-specific Global Security groups created and inspected.

**Confirmed findings:**

- Existing domain groups and employee memberships were examined.
- Nested group relationships were identified.
- Four role-specific Global Security groups were created.
- Group descriptions were populated.
- New groups had empty direct membership lists.

**Not demonstrated:**

- Administrative privilege assignment.
- Resource permission assignment.
- Effective resource access.
- Unauthorized group changes.
- Security incident activity.

**Final disposition:** Role-based identity structure established. No effective access or privilege assignment demonstrated.

## 12. Analyst Handoff

Day 06 is complete.

The four groups are existing lab objects and should not be recreated in subsequent exercises:

- `GG_HelpDesk_Tier1`
- `GG_HelpDesk_Tier2`
- `GG_SOC_Analysts`
- `GG_IT_Admins`

Day 07 can build on these objects while creating departmental Organizational Units and security groups.

Any later membership assignments, permissions, or security events must be documented under the exercise in which they were actually verified.

No additional Day 06 activity is required.
