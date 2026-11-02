# 90 Days of SOC Tier 1 and Tier 2 Mastery

### Building Practical SOC Analyst Skills Through Real Home Lab Investigations

**By WilliamInCyber**

## Project Overview

This repository documents my 90-day hands-on journey toward developing practical SOC Tier 1 and Tier 2 investigation skills, with a progression toward Detection Engineering.

I use my cybersecurity home lab to practice Active Directory administration, Windows endpoint security, SIEM investigations, alert triage, incident analysis, and detection engineering.

The objective is not simply to install tools or generate alerts. It is to understand system behavior, recognize suspicious patterns, investigate supporting evidence, and make defensible security decisions.

The project also incorporates simulated Managed Security Service Provider (MSSP) workflows, including customer communication, case handling, escalation, and service-level targets.

Each completed exercise is supported by technical documentation, genuine lab screenshots, verified observations, and investigation notes.

**Career direction:** Remote SOC Tier 1 Analyst, progressing toward Tier 2 investigations and Detection Engineering, including MSSP environments.

This is an independent learning project, not a claim of professional SOC or MSSP employment experience.

---

## Home Lab Environment

| Component | Technology | Purpose |
|---|---|---|
| Virtualization | Proxmox VE | Hosts the isolated corporate lab |
| Domain Controller | Windows Server 2022 | Active Directory and DNS |
| Windows Client | Windows 11 | Endpoint administration and security investigations |
| Active Directory Domain | `corp.local` | Identity and access management |
| SIEM | Splunk Enterprise | Log searching, correlation, and investigations |
| Security Monitoring | Wazuh | Endpoint monitoring and rule validation |
| Endpoint Telemetry | Windows Event Logs, Sysmon, PowerShell | Security event collection and analysis |
| Network Analysis | Wireshark, Suricata | Network visibility and investigation |
| Attacker Simulation | Kali Linux when required | Controlled security testing |

Tools are introduced only when required by an exercise. Planned integrations are not represented as already deployed or operational.

To manage limited hardware resources, I run only the virtual machines and monitoring tools needed for the current task.

---

## 90-Day Learning Roadmap

| Phase | Days | Focus | Capstone |
|---|---|---|---|
| 01 | 01 to 20 | Active Directory and Help Desk | Day 20 |
| 02 | 21 to 35 | Windows Endpoint Security | Day 35 |
| 03 | 36 to 45 | Splunk Investigation Mastery | Day 45 |
| 04 | 46 to 55 | Wazuh Monitoring and Rule Validation | Day 55 |
| 05 | 56 to 70 | Joint SOC x DFIR Investigations | Day 70 |
| 06 | 71 to 85 | Detection Engineering | Day 85 |
| 07 | 86 to 90 | Operation CORP Final SOC Simulation | Day 90 |

The roadmap preserves seven phases and seven practical capstones.

MSSP-related competencies are integrated into the existing exercises without extending the 90-day schedule.

---

## Phase 01: Active Directory and Help Desk

**Days 01 to 20**

Build practical knowledge of enterprise Windows identity administration and common Help Desk operations.

Focus areas include:

- Active Directory baseline verification
- Identity and computer inventory
- Organizational units, users, and security groups
- Password resets and account unlocks
- Windows domain membership and authentication
- Group Policy
- Shared folders and NTFS permissions
- DNS and connectivity troubleshooting
- Help Desk ticket simulations
- Administrative verification and documentation

**Capstone:** Day 20 Active Directory and Help Desk Practical Assessment.

[View Phase 01 Documentation](01-active-directory-help-desk/README.md)

### Exercise Progress

| Day | Exercise | Status | Documentation |
|---|---|---|---|
| 01 | Active Directory Baseline and Identity Inventory | Completed | [Day 01](01-active-directory-help-desk/day-01-ad-baseline/README.md) |
| 02 to 09 | Active Directory and Help Desk Exercises | Completed | See Phase 01 documentation |
| 10 | Password Resets & Account Unlocks | Current | Pending verification |
| 11 to 19 | Remaining Help Desk Exercises | Not started | Pending |
| 20 | Active Directory and Help Desk Capstone | Not started | Pending |

Individual documentation links will be added only after the corresponding exercises and repository paths have been verified.

---

## Phase 02: Windows Endpoint Security

**Days 21 to 35**

Investigate Windows authentication, processes, services, PowerShell activity, scheduled tasks, persistence mechanisms, and endpoint security telemetry.

Develop practical skills using Windows Security logs, Sysmon, and PowerShell event logging.

### Enterprise EDR Investigation Reasoning

Apply enterprise Endpoint Detection and Response investigation concepts using existing home lab telemetry.

Focus on:

- Process execution and parent-child relationships
- Command-line analysis
- Suspicious PowerShell activity
- User and host context
- Process timelines
- Persistence indicators
- Evidence correlation
- Investigation confidence and limitations
- Containment and escalation reasoning

These exercises simulate EDR investigation reasoning without claiming access to enterprise EDR products or capabilities that have not been verified.

**Capstone:** Day 35 Windows Endpoint Investigation.

**Status:** Not started.

---

## Phase 03: Splunk Investigation Mastery

**Days 36 to 45**

Practice log searching, filtering, event counting, distinct value analysis, timeline reconstruction, and event correlation using Splunk Search Processing Language (SPL).

Focus on:

- Authentication event analysis
- Process activity investigation
- Time-based patterns
- Event frequency and distinct values
- Cross-event correlation
- Evidence-based conclusions
- Investigation documentation

The priority is interpreting evidence rather than writing unnecessarily complex queries.

**Capstone:** Day 45 SIEM Investigation Assessment.

**Status:** Not started.

---

## Phase 04: Wazuh Monitoring and Rule Validation

**Days 46 to 55**

Explore endpoint monitoring, alert generation, rule behavior, alert validation, and the relationship between collected telemetry and security detections.

Focus on:

- Agent and telemetry verification
- Alert interpretation
- Rule identification
- Expected versus observed behavior
- Alert validation
- False-positive analysis
- Investigation evidence
- Detection limitations

**Capstone:** Day 55 Wazuh Monitoring Assessment.

**Status:** Not started.

---

## Phase 05: Joint SOC x DFIR Investigations

**Days 56 to 70**

Connect Active Directory, Windows endpoint activity, authentication evidence, and SIEM telemetry to investigate realistic security scenarios.

Practice collaboration between SOC triage and Digital Forensics and Incident Response (DFIR) investigation workflows.

Focus on:

- Alert intake and initial triage
- Incident severity classification
- Investigation hypotheses
- Evidence collection and correlation
- Timeline reconstruction
- Escalation decisions
- Case documentation
- Analyst handover
- Evidence gaps and limitations

### MSSP Operational Competencies

Four practical competencies are incorporated into the existing exercises.

**1. Customer-Facing Incident Reporting**

Prepare two reporting perspectives when appropriate:

- Internal technical investigation report
- Nontechnical customer-facing incident summary

Reports should distinguish confirmed facts, investigation findings, uncertainty, business impact, and recommended next steps.

**2. Simulated Multi-Customer Case Handling**

Use two fictional customer environments:

- `CORP-A`
- `CORP-B`

Practice customer identification, case separation, investigation context, and customer-specific reporting.

These are fictional case records. They do not represent actual customers or a production multi-tenant MSSP platform.

**3. Simulated SLA Management**

Practice acknowledgment prioritization using the following training targets:

| Severity | Acknowledgment Target |
|---|---|
| Critical | 15 minutes |
| High | 30 minutes |
| Medium | 2 hours |
| Low | 8 hours |

For each applicable scenario, document the simulated alert arrival time, acknowledgment time, severity, and whether the target was met.

These targets are training assumptions, not contractual service-level agreements.

**4. SOC and DFIR Collaboration**

Practice determining when an alert requires deeper investigation, additional evidence, containment recommendations, or escalation.

Document the reason for escalation and the information required by the receiving analyst.

**Capstone:** Day 70 Joint SOC x DFIR Investigation.

**Status:** Not started.

---

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
- Investigation usefulness
- Operational detection impact

Where relevant, assess whether detection logic helps analysts prioritize cases, reduce unnecessary alerts, identify meaningful activity, and make defensible decisions.

Document the trade-offs between detection coverage, false positives, telemetry availability, and operational effort.

**Capstone:** Day 85 Detection Engineering Assessment.

**Status:** Not started.

---

## Phase 07: Operation CORP

**Days 86 to 90**

Complete a final practical SOC simulation involving alert queue triage, investigation, case management, escalation, evidence handling, and analyst handover.

The final blind-shift assessment brings together skills developed throughout the roadmap.

### Assessment Areas

- Alert intake and prioritization
- Tier 1 triage decisions
- Tier 2 investigation reasoning
- Endpoint and identity evidence correlation
- Internal technical documentation
- Customer-facing incident communication
- Fictional customer case separation
- Simulated SLA acknowledgment tracking
- Escalation and handover
- Detection feedback and improvement recommendations

The objective is to demonstrate a complete SOC investigation workflow while clearly identifying any skills that remain partially demonstrated or untested.

**Final Capstone:** Day 90 Operation CORP Assessment.

**Status:** Not started.

---

## My SOC Investigation Methodology

I use a consistent investigation process throughout the project.

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

My investigation notes also follow a five-part reasoning structure:

**Observation → Interpretation → Correlation → Initial Disposition → Why**

A security alert is a starting point for investigation, not automatic proof of compromise.

---

## Documentation and Evidence Standards

Each completed exercise includes:

- A practical technical README
- Selected genuine home lab screenshots
- Professionally named evidence files
- Investigation notes using a Master Station Log
- Verified observations and interpretations
- Relevant evidence gaps and limitations
- An evidence-based outcome
- A documented next action

Screenshots are embedded directly into the daily README.

Evidence must come from actual exercises, not generated representations of completed lab activity.

### Evidence Integrity Rules

- No fabricated screenshots or investigation results
- No unsupported claims of detection success
- No publication of passwords, secrets, or sensitive credentials
- No simulated exercise presented as production SOC experience
- No planned tool integration described as operational without verification
- No completed day marked without supporting evidence

---

## MSSP Competency Evidence Matrix

The following matrix tracks practical competency development across the existing roadmap.

| Competency | Planned Days | Evidence Required | Current Status |
|---|---|---|---|
| Enterprise EDR investigation reasoning | 21 to 35 | Process analysis, telemetry correlation, investigation notes | Not yet tested |
| Customer-facing incident reporting | 56 to 70 | Internal report and customer summary | Not yet tested |
| Multi-customer case handling | 56 to 70 | Fictional CORP-A and CORP-B case records | Not yet tested |
| Simulated SLA management | 56 to 70 | Severity, acknowledgment timestamps, target comparison | Not yet tested |

### Assessment Definitions

**Demonstrated:** Practical exercise completed with verifiable supporting evidence.

**Partially demonstrated:** Some relevant skills or evidence exist, but the competency has not been fully validated.

**Not yet tested:** The competency has not yet been assessed through the required practical exercise.

Competency statuses will be updated as supporting evidence becomes available.

---

## Project Progress

**Completed:** 9 of 90 days

**Current phase:** Phase 01, Active Directory and Help Desk

**Latest completed exercise:** Day 09

**Current exercise:** Day 10, Password Resets & Account Unlocks

**Next milestone:** Day 20 Active Directory and Help Desk Capstone

**Overall status:** In progress

Progress is updated only after each practical exercise has been completed, verified, and documented.

---

## Project Outcomes

By the end of the program, the goal is to build a verifiable portfolio demonstrating practical ability in:

- Windows and Active Directory administration
- Security event analysis
- Endpoint investigation reasoning
- SIEM searching and correlation
- Alert triage and incident investigation
- SOC and DFIR collaboration
- Incident documentation and escalation
- Customer-oriented security reporting
- Simulated MSSP operational workflows
- Detection development and validation

These are intended learning outcomes, not claims that every competency has already been achieved.

---

## Author

**WilliamInCyber**

Cybersecurity learner building practical skills in SOC operations, Windows security investigations, SIEM monitoring, incident analysis, and detection engineering.

[GitHub Profile](https://github.com/WiLL75G)

---

*This repository documents an evidence-driven learning journey. All completed work is based on verified home lab exercises, and future activities are clearly identified as planned.*
  
