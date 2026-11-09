
# Day 05/90 | Active Directory Forests, Domains, and Organizational Units

## 90 Days of SOC Tier 1 & Tier 2 Mastery

**Phase:** 01 | Active Directory & Help Desk  
**Lab environment:** Proxmox VE, Windows Server 2022, Windows 11  
**Domain:** `corp.local`  
**Domain controller:** `DC01`  
**Investigation type:** Read-only Active Directory structure and identity placement assessment  
**Status:** Hands-on investigation completed

---

## 1. Investigation Overview

An Active Directory environment can be operational while its organizational structure remains poorly understood.

Before creating additional users, security groups, or policies, I wanted to understand how my existing domain was organized and where its identity objects were located.

On Day 05, I investigated the forest and domain hierarchy, domain and forest functional levels, Organizational Units (OUs), default containers, and the directory locations of an existing user and workstation.

My focus was not simply identifying objects in Active Directory. I wanted to understand why their locations matter for administration, Group Policy scope, and future SOC investigations.

This exercise built on Days 01 through 04, where I established the Active Directory baseline and verified DNS, domain controller discovery, and the Windows 11 domain secure channel.

## 2. Investigation Objectives

1. Identify the Active Directory forest and its root domain.
2. Verify the domain and forest functional levels.
3. Examine the existing OU hierarchy.
4. Distinguish an Organizational Unit from a default Active Directory container.
5. Identify an existing user account within the OU structure.
6. Verify the user's actual distinguished name.
7. Identify the domain-joined workstation's directory location.
8. Verify the computer object's actual distinguished name.
9. Explain the relevance of these findings to Help Desk administration and SOC investigations.

**Scope limitation:** This was a read-only investigation. I did not create additional domains, trusts, OUs, users, computers, or Group Policy Objects.

---

## 3. Lab Environment

| Component | Configuration |
|---|---|
| Virtualization platform | Proxmox VE |
| Domain controller | DC01 |
| Server operating system | Windows Server 2022 |
| Active Directory domain | corp.local |
| Domain controller FQDN | dc01.corp.local |
| Domain controller IP | 10.10.10.10 |
| Domain workstation | WIN11-CLIENT |
| Workstation IP | 10.10.10.20 |
| Workstation DNS | 10.10.10.10 |
| Administrative tools | Active Directory Domains and Trusts, Active Directory Users and Computers, Windows PowerShell |

The environment uses a single known Active Directory domain. The investigation was performed primarily through Windows graphical administration tools, with one PowerShell forest query.

---

## 4. Forest and Domain Discovery

### 4.1 Examining Active Directory Domains and Trusts

I opened **Active Directory Domains and Trusts** on DC01.

The console displayed `corp.local` as the domain available in the environment.

This provided an initial graphical view of the domain structure.

**Observed evidence:**
- Domain name: `corp.local`
- Displayed object type: `domainDNS`

**Analyst reasoning:**

The domain is the logical administrative and authentication boundary in which the investigated directory objects reside.

However, viewing a domain in the console alone does not establish the complete forest topology. I therefore performed an additional forest-level query.

### 4.2 Confirming the Forest Using PowerShell

I executed:

```powershell
Get-ADForest | Select-Object Name, RootDomain, Domains
```

The command returned:

```text
Name        RootDomain  Domains
----        ----------  -------
corp.local  corp.local  {corp.local}
```

**Verified findings:**

| Property | Result |
|---|---|
| Forest name | corp.local |
| Forest root domain | corp.local |
| Domains returned | corp.local |

**Interpretation:**

The query confirmed that `corp.local` is the forest root domain and the only domain returned by the forest query.

This is a single-domain forest according to the collected evidence.

I did not create another domain or test any interdomain or interforest trust relationships.

---

## 5. Domain and Forest Functional Levels

I opened the properties of `corp.local` in Active Directory Domains and Trusts.

The General tab displayed:

| Property | Observed value |
|---|---|
| DNS domain name | corp.local |
| Pre-Windows 2000 domain name | CORP |
| Domain functional level | Windows Server 2016 |
| Forest functional level | Windows Server 2016 |

### Interpretation

Functional levels define which Active Directory domain and forest capabilities are available and impose compatibility requirements for domain controllers.

Although DC01 runs Windows Server 2022, the displayed domain and forest functional levels are Windows Server 2016.

The server operating system version and Active Directory functional level are separate properties.

**No functional-level changes were made.**

Raising functional levels is an administrative operation that requires compatibility planning. It was not necessary for this investigation.

---

## 6. Examining the Existing OU Hierarchy

Using **Active Directory Users and Computers**, I examined the organizational structure under `corp.local`.

The existing `CorpOps` OU contained four visible child OUs:

- `Employees`
- `IT`
- `ServiceAccounts`
- `Workstations`

The domain also contained other visible objects and containers, including `Admins`, `Builtin`, `Computers`, `Domain Controllers`, and `Users`.

### Verified hierarchy

```text
corp.local
|
+-- CorpOps
|   |
|   +-- Employees
|   |   |
|   |   +-- Daniel Carter
|   |
|   +-- IT
|   |
|   +-- ServiceAccounts
|   |
|   +-- Workstations
|       |
|       +-- WIN11-CLIENT
|
+-- Admins
+-- Builtin
+-- Computers
+-- Domain Controllers
+-- Users
```

This represents the relevant objects observed during the investigation, not an exhaustive inventory of every Active Directory object.

### Analyst reasoning

OUs provide a way to organize directory objects for administrative delegation and Group Policy application.

The separation of employees, IT, service accounts, and workstations provides an existing organizational structure that can support different administrative responsibilities.

However, the OU names alone do not prove that particular permissions, delegation settings, or GPOs have been configured.

Those controls would require separate verification.

---

## 7. Organizational Units vs Default Containers

One important part of the investigation was distinguishing an OU from a default container.

### 7.1 Employees OU

I opened the Object tab of the `Employees` OU.

**Observed properties:**

| Property | Value |
|---|---|
| Canonical name | corp.local/CorpOps/Employees |
| Object class | Organizational Unit |
| Accidental deletion protection | Enabled |

This verified that `Employees` is an OU nested beneath `CorpOps`.

Its directory location can be represented as:

```text
OU=Employees,OU=CorpOps,DC=corp,DC=local
```

This representation follows from the observed canonical path and object class.

### 7.2 Users Container

I inspected the default `Users` object in the same domain.

**Observed properties:**

| Property | Value |
|---|---|
| Canonical name | corp.local/Users |
| Object class | Container |
| Accidental deletion protection | Disabled |

The container displayed built-in accounts and security groups, including `Administrator`, `Guest`, and `krbtgt`.

### Comparison

| Capability | Organizational Unit | Default Users Container |
|---|---|---|
| Organizes directory objects | Yes | Yes |
| Supports direct GPO links | Yes | No |
| Can support delegated administration | Yes | Yes, through permissions |
| Object class observed | Organizational Unit | Container |

**Investigation conclusion:**

The distinction matters because the location of an account or computer can affect which OU-linked Group Policies apply to it.

A user stored in the default `Users` container does not receive a GPO linked directly to that container, because that type of link is unsupported. Domain-level and other applicable policies may still affect the user.

I did not inspect actual GPO links or resultant policies during Day 05.

---

## 8. Investigating an Existing User Account

### 8.1 Locating Daniel Carter

Inside the `Employees` OU, I identified the existing user account **Daniel Carter**.

The Object tab confirmed the account's canonical directory location:

```text
corp.local/CorpOps/Employees/Daniel Carter
```

### 8.2 Verifying the Distinguished Name

I opened the account's Attribute Editor and inspected the `distinguishedName` attribute.

The String Attribute Editor displayed the complete value:

```text
CN=Daniel Carter,OU=Employees,OU=CorpOps,DC=corp,DC=local
```

This directly verified the account's location within the domain and nested OUs.

### 8.3 Understanding the DN Components

| Component | Meaning |
|---|---|
| CN=Daniel Carter | Common name of the user object |
| OU=Employees | Immediate parent OU |
| OU=CorpOps | Higher-level parent OU |
| DC=corp | First domain component |
| DC=local | Second domain component |

### 8.4 Additional Account Attributes Observed

During the read-only attribute inspection, I also observed:

| Attribute | Observed value |
|---|---|
| displayName | Daniel Carter |
| accountExpires | Never |
| badPwdCount | 0 |
| lockoutTime | 0 |
| logonCount | 7 |
| lastLogon | October 18, 2026, 3:57:57 AM Pacific Daylight Time |
| lastLogonTimestamp | October 12, 2026, 4:32:37 AM Pacific Daylight Time |

These values were observed in the administration console and were not modified.

**Analyst interpretation:**

The `lastLogon` attribute is maintained separately by domain controllers, while `lastLogonTimestamp` is replicated with a delay.

They should not be treated as equivalent timestamps or used alone to reconstruct a precise authentication timeline.

Similarly, `badPwdCount` and `lockoutTime` are useful account-state indicators, but do not replace Windows Security event logs when investigating authentication activity.

No suspicious activity was established by these values.

---

## 9. Investigating the Workstation Computer Object

### 9.1 Locating WIN11-CLIENT

I inspected the `Workstations` OU beneath `CorpOps`.

The existing domain workstation was present as a Computer object:

```text
WIN11-CLIENT
```

Its observed canonical directory path was:

```text
corp.local/CorpOps/Workstations/WIN11-CLIENT
```

### 9.2 Verifying the Computer Distinguished Name

Using the workstation object's Attribute Editor, I viewed its complete `distinguishedName` value.

**Directly verified result:**

```text
CN=WIN11-CLIENT,OU=Workstations,OU=CorpOps,DC=corp,DC=local
```

The Attribute Editor also displayed:

```text
dNSHostName: win11-client.corp.local
```

### Analyst reasoning

This confirmed that the workstation computer object belongs to the `Workstations` OU, separate from the `Employees` OU containing Daniel Carter.

The distinction is important because computer and user objects can be subject to different policy scopes and administrative responsibilities.

The distinguished name proves the computer object's directory placement at the time of inspection.

It does not independently establish the workstation's current connectivity, authentication activity, or effective Group Policy configuration.

Those require separate evidence.

---

## 10. Correlating User and Computer Directory Locations

The two directly verified distinguished names were:

**User:**

```text
CN=Daniel Carter,OU=Employees,OU=CorpOps,DC=corp,DC=local
```

**Computer:**

```text
CN=WIN11-CLIENT,OU=Workstations,OU=CorpOps,DC=corp,DC=local
```

### What these findings establish

Both objects belong to the `corp.local` domain.

Both objects are located beneath the `CorpOps` parent OU.

They are separated into different child OUs according to their object roles.

### What these findings do not establish

- Whether Daniel Carter has signed in to WIN11-CLIENT.
- Whether any specific GPO applies successfully.
- Whether an administrator has previously moved either object.
- Whether any OU delegation or access-control misconfiguration exists.
- Whether any malicious directory modification has occurred.

**Analyst conclusion:**

Directory structure is valuable investigation context, but object location alone is not evidence of compromise.

---

## 11. SOC and Help Desk Relevance

### Identity investigation

An analyst investigating an account can use its distinguished name to identify its current directory location and compare that location with the expected organizational structure.

### Group Policy troubleshooting

When users or computers are not receiving expected settings, OU placement is one relevant factor to examine alongside GPO links, security filtering, inheritance, permissions, and policy processing results.

### Directory change investigations

Unexpected changes to an object's distinguished name may indicate that it was moved or renamed.

An analyst would need historical directory-change evidence, such as appropriately configured auditing and relevant event logs, before concluding who made a change and why.

### Help Desk operations

Understanding the OU hierarchy prepares me for future exercises involving user administration, account lifecycle tasks, group-based access, and workstation management.

These skills will become increasingly important during the remaining Active Directory and Help Desk phase.

---

## 12. Evidence and Validation Summary

| Investigation check | Result |
|---|---|
| Domain visible in Domains and Trusts | Verified |
| Forest name and root domain queried | Verified |
| Forest domain list queried | Verified |
| Domain functional level | Verified |
| Forest functional level | Verified |
| Existing CorpOps OU hierarchy | Observed |
| Default Users container class | Verified |
| Nested Employees OU class and path | Verified |
| Daniel Carter user location | Verified |
| Daniel Carter complete distinguished name | Verified |
| WIN11-CLIENT computer location | Verified |
| WIN11-CLIENT complete distinguished name | Verified |
| Trust relationships tested | Not tested |
| Effective GPO application tested | Not tested |
| Historical object moves audited | Not tested |

All hands-on activities in this investigation were read-only.

### Screenshot evidence

The investigation was supported by genuine screenshots from the Windows Server 2022 lab, including:

1. Active Directory Domains and Trusts showing `corp.local`.
2. Domain properties displaying functional levels.
3. Forest query output in PowerShell.
4. Existing CorpOps OU hierarchy.
5. Users container properties and object class.
6. Employees OU properties and object class.
7. Daniel Carter's directory location and attributes.
8. Daniel Carter's complete distinguished name.
9. WIN11-CLIENT's directory location.
10. WIN11-CLIENT's complete distinguished name.

Screenshots will be added to the GitHub evidence directory during the evidence stage of the documentation workflow.

No screenshots are represented as already uploaded or committed here.

---

## 13. Limitations

This investigation was a structural assessment, not a security audit or a full Active Directory health assessment.

I did not inspect:

- Effective user or computer permissions.
- Delegated OU administration.
- Applied Group Policy results.
- Historical directory changes.
- Authentication event timelines.
- Interforest or interdomain trusts.
- Active Directory replication across multiple domain controllers.

The absence of suspicious findings in this read-only assessment does not prove the environment is free from security issues.

---

## 14. Key Lessons

**1. A forest is not the same as a domain.**

The forest defines the broader Active Directory directory and trust boundary. The domain provides an identity and administrative scope within that forest.

**2. Server operating system versions and functional levels are different.**

My domain controller runs Windows Server 2022, while the observed domain and forest functional levels are Windows Server 2016.

**3. OUs and default containers are not interchangeable.**

Both can hold directory objects, but only OUs support direct GPO links.

**4. Distinguished names provide precise directory context.**

The verified DNs show exactly where the investigated user and computer objects reside.

**5. A directory attribute is not a complete security investigation.**

Attributes such as `lastLogon`, `badPwdCount`, and `distinguishedName` provide context. Historical activity and security conclusions require additional telemetry.

**6. Verification must come before configuration changes.**

Understanding the existing directory structure helps avoid unnecessary or poorly scoped administrative changes.

---

## 15. Next Steps

Day 05 established a verified view of the existing forest, domain, and OU structure.

**Day 06: Users, Security Groups & Access Design**

The next exercise will build on this foundation by examining how user identities, security groups, and access requirements should be designed before further account and group administration.

The existing directory structure will provide the organizational context for that work.

---

## Investigation Outcome

**Day 05 hands-on assessment: Complete**

I verified the single-domain forest, observed both functional levels, investigated the existing OU hierarchy, distinguished OUs from default containers, and directly confirmed the distinguished names of an existing user and workstation.

The investigation produced a documented Active Directory structural baseline without modifying the environment.

**Core takeaway:** Before investigating or changing an Active Directory identity, understand where it belongs, how that location affects administration, and what the available evidence actually proves.
