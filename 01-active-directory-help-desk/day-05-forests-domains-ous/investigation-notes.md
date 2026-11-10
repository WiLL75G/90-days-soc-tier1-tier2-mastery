
# Day 05 | Active Directory Forests, Domains, and Organizational Units Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | Not recorded in the supplied Day 05 notes |
| Exercise | Active Directory Forests, Domains, and Organizational Units |
| Documentation Date | 2026-11-10 |
| Original Exercise Date | Not recorded |
| Environment | Proxmox VE, Windows Server 2022, Windows 11 |
| Domain Controller | DC01 |
| Domain | corp.local |
| Phase | 01 - Active Directory & Help Desk |
| Activity Type | Read-only directory structure assessment |
| Status | Completed |
| Disposition | Directory structure baseline documented. No security incident established. |

## 1. Initial Objective

Establish an evidence-based understanding of the existing Active Directory forest, domain, Organizational Unit hierarchy, and directory object locations.

The investigation focused on determining how the current environment was organized before proceeding with additional identity administration, security group design, and policy-related exercises.

The primary investigation question was:

How is the existing Active Directory forest organized, where are its user and computer objects located, and what does their placement establish for subsequent Help Desk operations and SOC investigations?

The assessment focused on:

- Forest and root domain identity
- Domain and forest functional levels
- Existing Organizational Unit hierarchy
- Differences between Organizational Units and default containers
- User account location and distinguished name
- Identity-related directory attributes
- Workstation computer object location and distinguished name
- Relationships between user and computer object placement

This was a planned read-only directory structure assessment, not an investigation triggered by a security alert.

### Initial Hypothesis

The existing environment contained a single Active Directory forest and domain, with Organizational Units separating employee identities from workstation computer objects.

This hypothesis required verification through the available Active Directory administration tools and directory attributes.

## 2. Systems and Evidence Sources

The investigation was conducted using the existing Active Directory environment.

### Environment

| Component | Documented Value |
| --- | --- |
| Hypervisor | Proxmox VE |
| Domain controller | DC01 |
| Server operating system | Windows Server 2022 |
| Domain | corp.local |
| Client operating system | Windows 11 |
| User account examined | Daniel Carter |
| Computer object examined | WIN11-CLIENT |

### Tools and Directory Sources

- Active Directory Domains and Trusts
- Active Directory Users and Computers (ADUC)
- Windows PowerShell
- Active Directory Attribute Editor
- Domain and forest properties
- Organizational Unit properties
- User object properties
- Computer object properties
- Directory distinguished name attributes
- Authentication-related directory attributes

The investigation used graphical administration tools and PowerShell to inspect the existing directory configuration.

No additional forest, domain, trust relationship, user account, security group, Organizational Unit, computer object, or policy was created during the exercise.

## 3. Actions and Verified Results

| Action | Verified Result |
| --- | --- |
| Inspect Active Directory Domains and Trusts | corp.local displayed |
| Query Active Directory forest | Forest name corp.local |
| Review forest root domain | corp.local |
| Review returned domain list | corp.local only |
| Inspect domain functional level | Windows Server 2016 |
| Inspect forest functional level | Windows Server 2016 |
| Inspect CorpOps hierarchy | Four child OUs visible |
| Inspect Employees object type | Organizational Unit |
| Inspect default Users object type | Container |
| Inspect Employees deletion protection | Enabled |
| Inspect Users container deletion protection | Disabled |
| Locate Daniel Carter | CorpOps Employees OU |
| Verify Daniel Carter distinguished name | Complete DN recorded |
| Inspect Daniel Carter attributes | Values recorded |
| Locate WIN11-CLIENT | CorpOps Workstations OU |
| Verify WIN11-CLIENT distinguished name | Complete DN recorded |
| Review workstation DNS hostname attribute | win11-client.corp.local |

The results established the observed directory structure and object attributes at the time of inspection.

The checks did not establish historical object movements, effective permissions, actual Group Policy application, or unauthorized directory changes.

## 4. Technical Evidence and Findings

### Finding 01: Active Directory Forest and Root Domain

**Verification method:** Active Directory Domains and Trusts and administrative PowerShell.

**Command executed:**

`Get-ADForest | Select-Object Name, RootDomain, Domains`

**Observed output:**

```text
Name        RootDomain  Domains
----        ----------  -------
corp.local  corp.local  {corp.local}
```

The graphical administration console also displayed `corp.local` with object type `domainDNS`.

**Observed:**

- Forest name: corp.local
- Forest root domain: corp.local
- Returned domain list: corp.local

**Interpretation:** The collected results support a single-domain Active Directory forest in the queried environment.

The forest root domain and the returned domain list were consistent.

**Limitation:** The query did not establish the health of every forest-related service.

Additional forests were not inspected, and interforest or interdomain trust relationships were not tested.

### Finding 02: Domain and Forest Functional Levels

**Verification method:** Active Directory Domains and Trusts properties.

**Observed:**

| Property | Recorded Value |
| --- | --- |
| Domain name | corp.local |
| Pre-Windows 2000 name | CORP |
| Domain functional level | Windows Server 2016 |
| Forest functional level | Windows Server 2016 |

**Interpretation:** Both the domain and forest functional levels were configured as Windows Server 2016.

The domain controller was running Windows Server 2022.

This establishes that the domain controller operating system version and the configured Active Directory functional levels were not identical.

**Limitation:** No functional-level changes were performed.

Compatibility requirements for potential future changes were not evaluated.

The observed values do not independently establish overall Active Directory health.

### Finding 03: Existing Organizational Unit Structure

**Verification method:** Active Directory Users and Computers.

**Observed:** The `CorpOps` Organizational Unit contained four visible child Organizational Units.

```text
corp.local
|
+-- CorpOps
    |
    +-- Employees
    +-- IT
    +-- ServiceAccounts
    +-- Workstations
```

Additional directory objects and containers were visible directly under the domain, including:

- Admins
- Builtin
- Computers
- Domain Controllers
- Users

**Interpretation:** The observed directory hierarchy provided separate organizational locations for employees, IT-related objects, service accounts, and workstations.

The OU names suggested intended administrative purposes.

However, naming conventions alone do not establish that specific permissions, administrative delegation, security restrictions, or Group Policy settings were configured.

**Limitation:** The investigation did not inventory every object within each OU.

OU delegation, linked GPOs, effective permissions, and policy inheritance were not tested.

### Finding 04: Organizational Unit Versus Default Container

**Verification method:** Inspection of directory object properties through ADUC.

**Observed:**

Employees object:

```text
Canonical name: corp.local/CorpOps/Employees
Object class: Organizational Unit
Accidental deletion protection: Enabled
```

Default Users object:

```text
Canonical name: corp.local/Users
Object class: Container
Accidental deletion protection: Disabled
```

The default Users container contained built-in accounts and security groups, including:

- Administrator
- Guest
- krbtgt

**Interpretation:** The inspected objects belonged to different Active Directory object classes.

The Employees object was an Organizational Unit.

The default Users object was a Container.

An Organizational Unit supports direct Group Policy linking and can serve as a scope for administrative delegation.

The default Users container does not support direct GPO linking, although its contained objects can still be affected by applicable domain-level policies.

This distinction is relevant when investigating object placement, policy scope, and administrative structure.

**Limitation:** The investigation established the observed object classes and properties.

No GPO application, inheritance, or effective delegation testing was performed.

### Finding 05: Daniel Carter User Object Location

**Verification method:** ADUC Object tab and Active Directory Attribute Editor.

**Observed:**

Canonical path:

```text
corp.local/CorpOps/Employees/Daniel Carter
```

Distinguished name:

```text
CN=Daniel Carter,OU=Employees,OU=CorpOps,DC=corp,DC=local
```

Object class:

`User`

**Interpretation:** Daniel Carter's user object was located in the Employees OU beneath the CorpOps parent OU.

The canonical path and distinguished name established the account's directory location at the time of inspection.

The distinguished name provides a structured identifier that can support subsequent directory object comparisons.

**Limitation:** The current distinguished name does not establish whether the user object was previously moved or renamed.

Effective permissions, direct and indirect group memberships, and applied Group Policy were not verified during this exercise.

### Finding 06: Existing User Directory Attributes

**Verification method:** Active Directory Attribute Editor.

**Account examined:** Daniel Carter.

**Observed:**

| Attribute | Recorded Value |
| --- | --- |
| `displayName` | Daniel Carter |
| `accountExpires` | Never |
| `badPwdCount` | 0 |
| `lockoutTime` | 0 |
| `logonCount` | 7 |
| `lastLogon` | October 18, 2026, 3:57:57 AM PDT |
| `lastLogonTimestamp` | October 12, 2026, 4:32:37 AM PDT |

**Interpretation:** The recorded attributes provided account configuration and authentication-related directory context.

The `lastLogon` attribute is maintained per domain controller and is not replicated between domain controllers.

The `lastLogonTimestamp` attribute is replicated but is not designed to provide an exact timestamp for every successful authentication.

The observed `logonCount`, `badPwdCount`, and `lockoutTime` values represent directory attribute states rather than a complete authentication event history.

These observations did not independently establish suspicious authentication activity.

**Limitation:** No Windows Security authentication events were correlated with the inspected attributes.

The investigation did not reconstruct a complete authentication timeline.

The recorded values do not establish the source workstation, logon type, authorization context, or historical pattern of individual authentication events.

No account modifications were performed.

### Finding 07: WIN11-CLIENT Computer Object Location

**Verification method:** ADUC computer object properties and Active Directory Attribute Editor.

**Observed:**

Computer object name:

`WIN11-CLIENT`

Object class:

`Computer`

Canonical path:

```text
corp.local/CorpOps/Workstations/WIN11-CLIENT
```

Distinguished name:

```text
CN=WIN11-CLIENT,OU=Workstations,OU=CorpOps,DC=corp,DC=local
```

DNS hostname attribute:

`win11-client.corp.local`

**Interpretation:** The WIN11-CLIENT computer object was located in the Workstations OU beneath CorpOps.

The object shared the same domain and parent OU as the Daniel Carter user object but belonged to a different child OU.

This established separate directory locations for the inspected user and computer objects.

**Limitation:** Directory object presence and location do not independently establish current endpoint connectivity, successful authentication, secure-channel health, or effective Group Policy application.

The computer object's modification timestamp does not independently identify which attribute changed or who performed the change.

## 5. Analyst Assessment

### Observed

- The Active Directory forest name was corp.local.
- The forest root domain was corp.local.
- The forest query returned only corp.local in its domain list.
- Domain and forest functional levels were Windows Server 2016.
- The CorpOps OU contained Employees, IT, ServiceAccounts, and Workstations.
- Employees was identified as an Organizational Unit.
- The default Users object was identified as a Container.
- Employees had accidental deletion protection enabled.
- The inspected Users container had accidental deletion protection disabled.
- Daniel Carter was located in the CorpOps Employees OU.
- Daniel Carter's complete distinguished name was recorded.
- Existing authentication-related attributes were inspected.
- WIN11-CLIENT was located in the CorpOps Workstations OU.
- WIN11-CLIENT's complete distinguished name and DNS hostname attribute were recorded.

These observations established the directory configuration and object locations examined during Day 05.

### Correlated

The forest query established `corp.local` as the forest root domain and the only domain returned in the forest domain list.

ADUC inspection identified the existing organizational hierarchy.

Object property inspection distinguished the Employees Organizational Unit from the default Users container.

The distinguished names provided structured confirmation of the user and computer object locations.

Verified user identity:

```text
CN=Daniel Carter,OU=Employees,OU=CorpOps,DC=corp,DC=local
```

Verified computer identity:

```text
CN=WIN11-CLIENT,OU=Workstations,OU=CorpOps,DC=corp,DC=local
```

Both objects shared the same domain components:

`DC=corp,DC=local`

Both objects were located beneath:

`OU=CorpOps`

Their child Organizational Units differed:

- Daniel Carter: Employees
- WIN11-CLIENT: Workstations

These findings established the relationship between the inspected directory objects.

They did not establish an authentication relationship between the user and workstation or prove that either object had previously been moved.

### Interpretation

The collected results support the documented Active Directory forest, domain, and OU structure.

The existing directory hierarchy provides separate organizational locations for user and computer objects.

An analyst can use canonical paths and distinguished names to establish where directory objects reside at the time of inspection.

This information can support subsequent investigations involving:

- Unexpected identity placement
- Account or computer object movement
- Group Policy scoping
- Administrative delegation
- Directory configuration changes
- Identity and computer object administration

However, an object's current distinguished name does not establish historical movement or modification.

Additional directory auditing or other reliable historical evidence would be required to determine whether an object was moved, who performed the action, and whether the action was authorized.

The inspected authentication-related attributes provided historical context but did not establish suspicious activity.

#### Initial Hypothesis Assessment

**Hypothesis:** The environment contains a single Active Directory forest and domain, with existing Organizational Units separating employee identities from workstation computer objects.

**Supporting observations:**

- The forest query returned corp.local as the forest root domain.
- The returned domain list contained only corp.local.
- The existing CorpOps hierarchy was visible.
- Daniel Carter was located in Employees.
- WIN11-CLIENT was located in Workstations.
- Both objects shared the CorpOps parent OU.

**Assessment:** Supported by the collected directory configuration and object-location evidence.

The findings did not establish effective administrative separation, Group Policy enforcement, or access restrictions between the inspected OUs.

### Unknown

The following areas were not fully validated during Day 05:

- Actual GPO links and effective policy results
- OU administrative delegation
- Effective user and computer permissions
- Historical account or computer object movement
- Whether Daniel Carter authenticated to WIN11-CLIENT
- Whether directory objects had previously been renamed
- Historical directory change attribution
- Whether any unobserved directory changes were unauthorized or malicious
- Complete forest-related service health
- Interforest or interdomain trust relationships

These areas require separate verification before stronger conclusions can be made.

## 6. Actions and Changes

This was a read-only Active Directory structure assessment.

The completed activities consisted of:

- Inspecting the existing domain through Active Directory Domains and Trusts
- Querying the forest configuration using PowerShell
- Reviewing domain and forest functional levels
- Inspecting the existing CorpOps OU hierarchy
- Comparing an Organizational Unit with the default Users container
- Verifying the Daniel Carter user object's directory location
- Reviewing selected Daniel Carter directory attributes
- Verifying the WIN11-CLIENT computer object's directory location
- Comparing the user and computer distinguished names
- Documenting the observed results and investigation limitations

**Configuration changes:** None.

**Objects created or deleted:** None.

**Additional domains or trusts created:** None.

**Group Policy changes:** None.

**Service restarts or repairs:** None.

**Remediation:** None performed.

No investigation-blocking error was documented in the supplied Day 05 record.

The exercise remained within the approved read-only assessment scope.

## 7. Classification and Disposition

**Classification:** Planned Active Directory directory structure assessment.

**Disposition:** Forest, domain, Organizational Unit, and selected directory object baseline completed and documented.

**Security Conclusion:** No suspicious activity or security incident was established from the collected evidence.

**Confidence:** High for the directly observed forest configuration, functional levels, object classes, directory paths, and inspected attribute values.

**Evidence Limitations:** Insufficient evidence to establish historical directory changes, unauthorized activity, effective permissions, or authentication relationships.

The investigation established the current directory structure within the scope of the performed checks.

The observed account attributes and object locations did not independently demonstrate compromise or policy violations.

No remediation or configuration changes were justified by the documented findings.

## 8. Verification and Evidence Gaps

### Completed Verification

| Verification Area | Result |
| --- | --- |
| Forest identity | corp.local verified |
| Forest root domain | corp.local verified |
| Returned domain list | corp.local only |
| Domain functional level | Windows Server 2016 |
| Forest functional level | Windows Server 2016 |
| CorpOps hierarchy | Inspected |
| Employees object class | Organizational Unit |
| Users object class | Container |
| Daniel Carter location | Verified |
| Daniel Carter distinguished name | Verified |
| Selected user attributes | Recorded |
| WIN11-CLIENT location | Verified |
| WIN11-CLIENT distinguished name | Verified |
| WIN11-CLIENT DNS hostname attribute | Recorded |

### Outstanding Verification

The investigation did not establish:

- Effective Group Policy application
- OU delegation and administrative permissions
- Complete authorization paths
- Historical user or computer object movement
- User-to-workstation authentication activity
- Whether historical directory changes were authorized
- Interforest or interdomain trust behavior
- Comprehensive Active Directory forest health

These items were outside the completed Day 05 scope.

They must not be presented as completed validations or confirmed security failures.

The verified directory structure should be treated as an initial configuration baseline rather than a complete Active Directory security assessment.

## 9. Handover Notes

An analyst continuing this work should use the documented Day 05 findings as the starting reference for subsequent identity, access, and Active Directory structure investigations.

### Baseline Reference

| Item | Documented State |
| --- | --- |
| Forest | corp.local |
| Forest root domain | corp.local |
| Returned domain list | corp.local |
| Domain functional level | Windows Server 2016 |
| Forest functional level | Windows Server 2016 |
| Parent OU | CorpOps |
| Child OUs | Employees, IT, ServiceAccounts, Workstations |
| Employees object class | Organizational Unit |
| Default Users object class | Container |
| User object | Daniel Carter |
| User location | CorpOps Employees OU |
| Computer object | WIN11-CLIENT |
| Computer location | CorpOps Workstations OU |
| Investigation disposition | Structural baseline documented |

### Investigation Considerations

When reviewing subsequent directory changes, compare current observations against the documented Day 05 baseline.

Determine:

- Whether the forest or domain configuration has changed.
- Whether the functional levels have changed.
- Whether new Organizational Units have been created.
- Whether directory objects have moved between Organizational Units.
- Whether existing objects have been renamed or modified.
- Whether Group Policy links or delegation settings have changed.
- Whether supporting audit records establish who performed a change.
- Whether the change was authorized.

The documented distinguished names provide reference points for object placement.

A difference from the baseline does not automatically establish malicious activity.

Historical auditing, administrative records, or other reliable evidence would be required before attributing changes to an actor or concluding that activity was unauthorized.

### Outstanding Verification

The investigation did not comprehensively validate GPO application, administrative delegation, effective permissions, authentication relationships, or historical directory modifications.

These areas should be examined during an approved future exercise when relevant.

Previously completed Day 05 checks should not be repeated unless a new investigation, configuration change, or troubleshooting requirement makes revalidation necessary.

## 10. Next Action

Proceed to Day 06 | Users, Security Groups & Access Design under the approved 90 Days of SOC Tier 1 and Tier 2 Mastery roadmap.

Use the documented Day 05 forest, domain, and Organizational Unit hierarchy as the baseline.

The next exercise will focus on understanding identity roles, security group purposes, and intended access requirements within the existing environment.

Do not represent unperformed access configuration or permission testing as completed Day 05 work.

## Documentation Principle

Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.
