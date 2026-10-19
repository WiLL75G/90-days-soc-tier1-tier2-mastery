
# 90 Days of SOC Tier 1 and Tier 2 Mastery

### Building Practical SOC Analyst Skills Through Real Home Lab Investigations

**By WilliamInCyber**

## Project Overview

This repository documents my 90-day hands-on journey toward developing practical SOC Tier 1 and Tier 2 analyst skills.

I am using my own cybersecurity home lab to practice Active Directory administration, Windows endpoint security, SIEM investigations, alert triage, incident analysis, and detection engineering.

The objective is not simply to install tools or generate alerts. It is to understand system behavior, recognize suspicious patterns, investigate supporting evidence, and make defensible security decisions.

Each completed exercise includes technical documentation, genuine lab screenshots, and investigation notes.

## Home Lab Environment

| Component | Technology | Purpose |
| --- | --- | --- |
| Virtualization | Proxmox VE | Hosts the isolated corporate lab |
| Domain Controller | Windows Server 2022 | Active Directory and DNS |
| Windows Client | Windows 11 | Endpoint administration and security investigations |
| Active Directory Domain | `corp.local` | Identity and access management |
| SIEM | Splunk Enterprise | Log searching, correlation, and investigations |
| Security Monitoring | Wazuh | Endpoint monitoring and rule validation |
| Endpoint Telemetry | Windows Event Logs, Sysmon, PowerShell | Security event collection and analysis |
| Network Analysis | Wireshark, Suricata | Network visibility and investigation |
| Attacker Simulation | Kali Linux when required | Controlled activity in the lab |

Tools are introduced only when required by the exercise. Planned integrations are not represented as already deployed.

To manage limited hardware resources, I run only the virtual machines and monitoring tools required for the current task.

## 90-Day Learning Roadmap

| Phase | Days | Focus | Capstone |
| --- | --- | --- | --- |
| 01 | 01 to 20 | Active Directory and Help Desk | Day 20 |
| 02 | 21 to 35 | Windows Endpoint Security | Day 35 |
| 03 | 36 to 45 | Splunk Investigation Mastery | Day 45 |
| 04 | 46 to 55 | Wazuh Monitoring and Rule Validation | Day 55 |
| 05 | 56 to 70 | Integrated SOC Tier 1 and Tier 2 Investigations | Day 70 |
| 06 | 71 to 85 | Detection Engineering | Day 85 |
| 07 | 86 to 90 | Operation CORP Final SOC Simulation | Day 90 |

## Phase 01: Active Directory and Help Desk

**Days 01 to 20**

Focus areas include:

- Active Directory baseline verification
- Identity and computer inventory
- Organizational units, users, and security groups
- Password resets and account administration
- Windows domain membership and authentication
- Group Policy
- Shared folders and NTFS permissions
- DNS and connectivity troubleshooting
- Help Desk ticket simulations
- Practical assessment on Day 20

[View Phase 01 Documentation](01-active-directory-help-desk/README.md)

### Completed Exercises

| Day | Exercise | Status | Documentation |
| --- | --- | --- | --- |
| 01 | Active Directory Baseline and Identity Inventory | Completed | [Day 01](01-active-directory-help-desk/day-01-ad-baseline/README.md) |
| 02 | Identity Inventory | Not started | Pending |

Additional exercises will be linked after completion.

## Phase 02: Windows Endpoint Security

**Days 21 to 35**

Investigate Windows authentication, processes, services, PowerShell activity, scheduled tasks, persistence mechanisms, and endpoint security telemetry.

Develop practical skills using Windows Security logs, Sysmon, and PowerShell event logging.

**Capstone:** Day 35 Windows Endpoint Investigation.

**Status:** Not started.

## Phase 03: Splunk Investigation Mastery

**Days 36 to 45**

Practice log searching, filtering, event counting, distinct value analysis, timeline reconstruction, and event correlation using Splunk Search Processing Language.

Focus on interpreting evidence rather than writing unnecessarily complex queries.

**Capstone:** Day 45 SIEM Investigation Assessment.

**Status:** Not started.

## Phase 04: Wazuh Monitoring and Rule Validation

**Days 46 to 55**

Explore endpoint monitoring, alert generation, rule behavior, alert validation, and the relationship between collected telemetry and security detections.

**Capstone:** Day 55 Wazuh Monitoring Assessment.

**Status:** Not started.

## Phase 05: Integrated SOC Investigations

**Days 56 to 70**

Connect Active Directory, Windows endpoint activity, authentication evidence, and SIEM telemetry to investigate realistic SOC scenarios.

Practice triage, investigation, evidence correlation, escalation decisions, and case documentation.

**Capstone:** Day 70 Integrated SOC Investigation.

**Status:** Not started.

## Phase 06: Detection Engineering

**Days 71 to 85**

Develop and validate detection logic using controlled activity and observable telemetry.

Focus on:

- Detection hypotheses
- Baseline behavior
- Event selection and correlation
- Detection rule validation
- False positives and false negatives
- Threshold tuning
- Detection coverage and limitations

**Capstone:** Day 85 Detection Engineering Assessment.

**Status:** Not started.

## Phase 07: Operation CORP

**Days 86 to 90**

Complete a final practical SOC simulation involving alert queue triage, investigation, case management, escalation, evidence handling, and analyst handover.

The objective is to demonstrate a complete SOC investigation workflow using the skills developed throughout the program.

**Final Capstone:** Day 90 Operation CORP Assessment.

**Status:** Not started.

## My SOC Investigation Methodology

I use a consistent investigation process throughout the project:

1. Establish expected behavior and baseline activity.
2. Generate or identify observable activity.
3. Compare expected and actual telemetry.
4. Form an initial investigation hypothesis.
5. Triage relevant events.
6. Correlate evidence across available sources.
7. Separate observations from interpretations.
8. Identify missing evidence and limitations.
9. Determine an evidence-based disposition.
10. Document findings and review detection opportunities.

A security alert is a starting point for investigation, not automatic proof of compromise.

## Documentation and Evidence Standards

Each completed day includes:

- A practical technical README
- Selected genuine home lab screenshots
- Professionally named evidence files
- Investigation notes using a Master Station Log
- Verified observations, interpretations, and evidence gaps
- A documented outcome and next action

Screenshots are embedded directly into the daily README.

No fabricated evidence, unsupported investigation claims, passwords, or secrets are published.

## Project Progress

**Completed:** 1 of 90 days

**Current phase:** Active Directory and Help Desk

**Latest completed exercise:** Day 01, Active Directory Baseline and Identity Inventory

**Next exercise:** Day 02, Identity Inventory

This repository will be updated as practical exercises are completed and verified.

## Author

**WilliamInCyber**

Cybersecurity learner focused on practical SOC operations, investigation methodology, security monitoring, and detection engineering.

[GitHub Profile](https://github.com/WiLL75G)
  
