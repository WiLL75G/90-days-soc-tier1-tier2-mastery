
# Day 01 | Active Directory Baseline & Identity Inventory

**Project:** 90 Days of SOC Tier 1 & Tier 2 Mastery  
**Phase:** 01 — Active Directory & Help Desk  
**Status:** Hands-on complete  
**Lab domain:** `corp.local`

## Objective

Establish and document the existing Active Directory baseline before making changes to users, groups, computers, or access controls. Identify the domain controller, verify the AD DS service, and inspect existing identity and computer objects.

## Lab Environment

| Component | Configuration |
| --- | --- |
| Hypervisor | Proxmox VE |
| Domain controller | DC01, Windows Server 2022 |
| Domain | `corp.local` |
| AD management tool | Active Directory Users and Computers |
| Client | Windows 11 computer object in the Workstations OU |

## Practical Work Performed

### 1. Domain Controller Verification

Executed the following commands in an administrative PowerShell session:

```powershell
hostname
(Get-CimInstance Win32_ComputerSystem).Domain
Get-Service NTDS
```

**Verified results:**

- Hostname: `dc01`
- Domain: `corp.local`
- NTDS service: `Running`

### 2. Organizational Unit Inventory

Used Active Directory Users and Computers to inspect the existing domain structure.

```text
corp.local
├── Admins                         [Empty]
├── CorpOps
│   ├── Employees                  [Daniel Carter]
│   ├── IT                         [Empty]
│   ├── ServiceAccounts            [Empty]
│   └── Workstations               [Windows 11 computer object]
├── Domain Controllers             [DC01]
├── Computers                      [Empty]
└── Users                          [Default accounts and groups]
```

This is a simplified inventory of the containers inspected, not an exhaustive representation of every default Active Directory container.

### 3. Identity and Computer Inventory

- Identified an existing employee account, Daniel Carter, in the Employees OU.
- Confirmed the IT, ServiceAccounts, and Admins OUs were empty at the time of inspection.
- Located the Windows 11 computer object under Workstations.
- Located DC01 in the Domain Controllers OU, displayed as a global catalog.
- Inspected the default Users container and identified built-in accounts and security groups.
- Confirmed the default Computers container was empty.

## Evidence

The following genuine home-lab screenshots will be stored in the `evidence/` folder:

| Evidence | Purpose |
| --- | --- |
| `day-01-ntds-service-running.png` | Verify the AD DS service status |
| `day-01-ad-ou-structure.png` | Show the organizational structure |
| `day-01-employee-account-inventory.png` | Show the existing employee account |
| `day-01-workstation-computer-account.png` | Show the workstation computer object |

Screenshots will be embedded after the evidence files are uploaded.

## SOC Analyst Relevance

An identity baseline helps an analyst understand which accounts, groups, and computer objects exist before investigating suspicious changes. It also provides context for distinguishing expected administrative activity from changes requiring investigation.

## Observations and Limitations

**Observed:** The NTDS service was running, the domain was identified as `corp.local`, and the listed Active Directory objects were visible in ADUC.

**Not established by this exercise:** Full security-group memberships, successful authentication for every account, DNS health, Group Policy application, or end-to-end domain connectivity. These require separate verification.

## Outcome

Completed the initial Active Directory baseline and identity inventory without modifying existing domain objects. The documented findings provide a starting reference for subsequent identity administration, troubleshooting, and security investigations.
  
