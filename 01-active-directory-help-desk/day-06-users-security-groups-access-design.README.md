
# Day 06 | Users, Security Groups & Access Design

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Phase:** 01 Active Directory & Help Desk  
**Status:** Hands-on complete  
**Lab domain:** `corp.local`

## Objective

Investigate how Active Directory users, security groups, group scopes, and membership relationships support enterprise access design.

Inspect existing domain groups and employee memberships before creating four role-specific security groups for IT administration, SOC analysts, and Help Desk support.

Distinguish between group existence, group membership, assigned permissions, and effective access.

## Lab Environment

| Component | Configuration |
| --- | --- |
| Hypervisor | Proxmox VE |
| Domain controller | DC01, Windows Server 2022 |
| Domain | `corp.local` |
| AD management tool | Active Directory Users and Computers (ADUC) |
| Organizational Unit | `corp.local/CorpOps/IT` |
| Existing employee | Daniel Carter |

## Practical Work Performed

### 1. Existing Security Group Inspection

Used Active Directory Users and Computers to inspect existing domain security groups.

**Verified results:**

- Domain Admins was configured as a Global Security group.
- The built-in Administrator account appeared as a direct member of Domain Admins.
- Domain Users was also configured as a Global Security group.
- Both groups shared the same scope and type but served different security purposes.

This established that group scope and type alone do not determine administrative privileges.

### 2. Employee Group Membership Inspection

Inspected the existing Daniel Carter account under:

`corp.local/CorpOps/Employees`

**Verified results:**

- Domain Users appeared in the Member Of tab.
- Domain Users was identified as the account's primary group.
- No additional direct group memberships were shown in the captured view.

This demonstrated the difference between an account's Organizational Unit location and its security group membership.

### 3. Nested Group Relationships

Inspected the built-in Users group and its membership relationships.

**Verified results:**

- Domain Users appeared as a member.
- Authenticated Users appeared as a member.
- INTERACTIVE appeared as a member.
- Domain Admins was also observed as a member of the built-in Administrators group.

The inspected relationships included:

```text
Daniel Carter
    |
    v
Domain Users
    |
    v
Builtin Users
```

These relationships demonstrated why checking only an account's directly visible memberships may provide an incomplete picture of its authorization context.

The inspection did not establish effective access to any specific resource.

### 4. Organizational Unit Baseline

Reviewed the existing Active Directory structure before creating new groups.

```text
corp.local
├── Admins
└── CorpOps
    ├── Employees
    ├── IT
    ├── ServiceAccounts
    └── Workstations
```

**Verified results:**

- The IT OU appeared empty before group creation.
- The ServiceAccounts OU appeared empty.
- The Admins OU appeared empty.
- The IT OU was selected to organize the new security groups.

### 5. Role-Specific Security Group Creation

Created four groups under:

`corp.local/CorpOps/IT`

| Group | Scope | Type | Intended Role |
| --- | --- | --- | --- |
| `GG_HelpDesk_Tier1` | Global | Security | Initial user support and account troubleshooting |
| `GG_HelpDesk_Tier2` | Global | Security | Escalated technical support |
| `GG_SOC_Analysts` | Global | Security | Security monitoring and investigations |
| `GG_IT_Admins` | Global | Security | Authorized IT administration |

**Verified results:**

- All four groups were created in the IT OU.
- Each group was configured with Global scope and Security type.
- Group properties were inspected through ADUC.
- Descriptions were added to document their intended responsibilities.

### 6. Group Membership Verification

Inspected the Members tabs of the newly created groups.

**Verified results:**

- `GG_HelpDesk_Tier1`: No direct members observed.
- `GG_HelpDesk_Tier2`: No direct members observed.
- `GG_SOC_Analysts`: No direct members observed.
- `GG_IT_Admins`: No direct members observed.

The groups existed, but no user or nested group assignments were demonstrated.

### 7. Final Group Inventory and Object Inspection

Reviewed the final IT OU inventory.

**Verified results:**

- All four role-specific groups appeared in the IT OU.
- Group descriptions were populated.
- `GG_IT_Admins` had the canonical path `corp.local/CorpOps/IT/GG_IT_Admins`.
- The inspected object class was Group.
- Accidental deletion protection was unchecked in the captured properties.

The group names and descriptions documented intended responsibilities but did not establish administrative privileges.

### 8. Access Design Concept

Reviewed the AGDLP access design model:

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

This model separates identity role membership from resource permission assignment.

Day 06 introduced the concept but did not implement a complete AGDLP permission chain.

No Domain Local resource groups, shared folder permissions, or effective access tests were configured during this exercise.

## Evidence

All screenshots below were collected from my actual Active Directory home lab during Day 06.

### Evidence 01: Domain Admins Membership

![Domain Admins membership showing Administrator](evidence/day-06-01-domain-admins-membership.png)

**Observation:** The Domain Admins group contained the built-in Administrator account as a direct member at inspection time.

### Evidence 02: Employee Group Membership

![Daniel Carter group membership](evidence/day-06-02-daniel-carter-group-membership.png)

**Observation:** Daniel Carter's Member Of tab displayed Domain Users as the account's primary group.

### Evidence 03: Built-in Users Group Membership

![Built-in Users group membership](evidence/day-06-03-builtin-users-group-membership.png)

**Observation:** The built-in Users group contained Domain Users, Authenticated Users, and INTERACTIVE as members.

### Evidence 04: Help Desk Security Group Configuration

![Help Desk Tier 1 group properties](evidence/day-06-04-helpdesk-tier1-group-properties.png)

**Observation:** GG_HelpDesk_Tier1 was configured as a Global Security group in the IT Organizational Unit.

### Evidence 05: IT Administrators Group Membership

![IT Administrators group empty membership](evidence/day-06-05-it-admins-empty-membership.png)

**Observation:** GG_IT_Admins had no direct members in the inspected Members tab.

### Evidence 06: Final IT Security Group Inventory

![Final IT security group inventory](evidence/day-06-06-final-it-group-inventory.png)

**Observation:** The IT OU contained all four newly created Global Security groups with populated descriptions.

## SOC Analyst Relevance

Active Directory security group relationships are important when investigating identity-related alerts and suspected privilege misuse.

This exercise supports investigations involving:

- Unexpected membership in privileged groups.
- Unauthorized additions to security groups.
- Suspicious account privilege changes.
- Nested group relationships that may affect authorization.
- Differences between intended roles and effective permissions.
- Potential misuse of administrative accounts.

A SOC analyst should distinguish between:

1. A security group existing in Active Directory.
2. An account belonging to that group.
3. The group having permissions over a resource.
4. An account actually exercising those permissions.

For example, the name `GG_IT_Admins` does not prove that its members have Domain Admin privileges.

These are potential investigation use cases, not security incidents detected during Day 06.

## Observations and Limitations

**Observed:** Existing domain groups and employee membership were inspected, nested group relationships were identified, and four role-specific Global Security groups were created in the IT OU.

The newly created groups had populated descriptions and empty direct membership lists at inspection time.

**Not yet verified:**

- Assigned NTFS or shared folder permissions.
- Delegated Active Directory administrative rights.
- Effective administrative privileges.
- Successful or denied resource access.
- Security event logs associated with group creation.
- Historical group membership changes.
- Implementation of a complete AGDLP permission chain.

Current group membership screenshots establish the observed membership state, not when or why that state was established.

These areas require separate configuration and validation during subsequent exercises.

## Outcome

Completed the Active Directory users, security groups, and access design exercise.

Investigated existing security groups, employee memberships, and nested group relationships before creating four role-specific Global Security groups in the IT OU.

Verified group configuration, descriptions, and empty direct membership lists.

**Final disposition:** Role-based group structure established. No administrative privilege assignments or effective resource access demonstrated.

No security incident was identified or confirmed during this exercise.

## Next Exercise

**Day 07 | Create OUs & Security Groups**

Continue the approved Active Directory learning plan by creating departmental Organizational Units and security groups, building on the existing Day 06 structure without repeating completed work.
