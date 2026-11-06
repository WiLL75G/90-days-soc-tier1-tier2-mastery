
# Day 04 | DNS and Domain Functionality Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | SOC-AD-D04-001 |
| Exercise | DNS and Domain Functionality Verification |
| Documentation Date | 2026-11-06 |
| Original Exercise Date | 2026-10-20 |
| Environment | Proxmox VE, Windows 11, Windows Server 2022 |
| Workstation | WIN11-CLIENT |
| Domain Controller | DC01 |
| Domain | corp.local |
| Workstation IPv4 | 10.10.10.20 |
| Domain Controller IPv4 | 10.10.10.10 |
| Activity Type | Planned client-side domain functionality verification |
| Status | Completed |
| Disposition | Client-side DNS, domain discovery, and secure-channel verification successful. No security incident established. |

## 1. Initial Objective

Verify that the existing Windows 11 workstation could use the Active Directory domain infrastructure established during the preceding exercises.

Day 03 examined domain controller services, DNS resolution, domain discovery, and targeted diagnostic results directly on DC01.

Day 04 extended the baseline to WIN11-CLIENT.

The exercise focused on:

- Workstation network and DNS configuration
- Domain controller hostname resolution
- Domain controller discovery
- Workstation domain secure-channel verification
- Correlation with the previous domain controller findings

This was a planned infrastructure verification exercise, not a response to a security alert.

The objective was to establish a reliable client-side domain functionality baseline without modifying the existing configuration.

## 2. Systems and Evidence Sources

- Proxmox VE virtualization environment
- WIN11-CLIENT running Windows 11
- DC01 running Windows Server 2022
- Active Directory domain: corp.local
- Windows PowerShell
- Windows TCP/IP configuration
- DNS resolution results
- Domain controller discovery results
- Workstation secure-channel verification results
- Previously documented Day 03 findings

The investigation commands were executed on WIN11-CLIENT.

### Network Environment

| Component | Verified or Established Value |
| --- | --- |
| Workstation hostname | WIN11-CLIENT |
| Operating system | Windows 11 |
| Workstation IPv4 | 10.10.10.20 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 10.10.10.1 |
| DNS server | 10.10.10.10 |
| Domain controller | DC01 |
| Domain controller IPv4 | 10.10.10.10 |
| Domain | corp.local |

An initial network configuration command was executed on DC01 rather than WIN11-CLIENT.

The incorrect execution context was identified from the hostname.

The command was subsequently executed on the intended workstation, and the DC01 result was excluded from the workstation baseline.

Exact command execution timestamps were not independently recorded for every command.

## 3. Actions and Verified Results

| Action | Verified Result |
| --- | --- |
| Inspect workstation network configuration | Expected settings observed |
| Verify configured DNS server | 10.10.10.10 |
| Resolve `dc01.corp.local` | Successful |
| Verify returned IPv4 address | 10.10.10.10 |
| Discover domain controller | Successful |
| Verify workstation secure channel | True |
| Compare results with Day 03 | Client-side checks successful; earlier DC01 diagnostic discrepancy unresolved |

All four planned workstation verification checks completed successfully.

The results established the tested workstation's network configuration, hostname resolution, domain controller discovery, and secure-channel status.

The investigation did not establish complete authentication, authorization, or Group Policy functionality.

## 4. Technical Evidence and Findings

### Finding 01: Workstation Network Configuration

**Verification method:** Windows network configuration inspection.

**Command executed:**

`ipconfig /all`

**Observed:**

| Property | Recorded Value |
| --- | --- |
| Hostname | win11-client |
| Primary DNS suffix | corp.local |
| IPv4 address | 10.10.10.20 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 10.10.10.1 |
| DNS server | 10.10.10.10 |
| DHCP | Disabled |

**Interpretation:** WIN11-CLIENT was configured with the expected addressing information for the existing CORP lab network.

The DNS server pointed to DC01 at `10.10.10.10`.

This established the workstation's configured DNS destination.

**Limitation:** Correct network configuration does not independently establish successful DNS resolution, domain controller discovery, or domain authentication.

### Finding 02: Domain Controller Hostname Resolution

**Verification method:** PowerShell DNS query.

**Command executed:**

`Resolve-DnsName dc01.corp.local`

**Observed:**

| Property | Recorded Value |
| --- | --- |
| Queried hostname | dc01.corp.local |
| Record type | A |
| Returned IPv4 | 10.10.10.10 |
| TTL | 3600 seconds |
| Result | Successful |

**Interpretation:** WIN11-CLIENT successfully resolved the domain controller hostname to the expected IPv4 address.

This extended the DNS verification performed directly on DC01 during Day 03 to a separate Windows workstation.

The result supported successful hostname resolution within the tested environment.

**Limitation:** Successful hostname resolution does not independently establish successful domain controller discovery, user authentication, or workstation trust health.

### Finding 03: Domain Controller Discovery

**Verification method:** `nltest` domain controller discovery.

**Command executed:**

`nltest /dsgetdc:corp.local`

**Observed output:**

```text
DC: \\dc01.corp.local
Address: \\10.10.10.10
Dom Name: corp.local
Forest Name: corp.local
Dc Site Name: Default-First-Site-Name
Our Site Name: Default-First-Site-Name
The command completed successfully
```

The returned flags included LDAP, KDC, PDC, GC, writable directory services, and DNS-related capabilities.

**Interpretation:** WIN11-CLIENT successfully discovered DC01 as the domain controller for `corp.local`.

The discovery result provided a separate verification from ordinary DNS hostname resolution.

The returned service flags identified capabilities advertised by the discovered domain controller.

**Limitation:** Successful discovery and advertised capabilities do not independently establish successful interactive user authentication, Kerberos ticket issuance, or complete domain health.

### Finding 04: Workstation Domain Secure Channel

**Verification method:** PowerShell secure-channel verification.

**Command executed:**

`Test-ComputerSecureChannel -Verbose`

**Observed output:**

```text
VERBOSE: Performing the operation "Test-ComputerSecureChannel" on target "WIN11-CLIENT".
True
VERBOSE: The secure channel between the local computer and the domain corp.local is in good condition.
```

**Observed:** The command returned `True`.

The verbose output reported that the workstation's secure channel with `corp.local` was in good condition.

**Interpretation:** The result provided direct verification that the workstation domain secure channel was healthy at the time of testing.

This supported the conclusion that the tested workstation trust relationship was functioning.

**Limitation:** Successful secure-channel verification does not independently establish that every domain authentication mechanism, Kerberos operation, Group Policy process, or access control is functioning correctly.

### Finding 05: Correlation With Day 03 Domain Controller Diagnostics

**Verification method:** Comparison of Day 04 workstation results with previously documented Day 03 findings.

**Previous Day 03 command executed on DC01:**

`nltest /sc_verify:corp.local`

**Previous observed result:**

```text
I_NetLogonControl failed: Status = 1355 0x54b ERROR_NO_SUCH_DOMAIN
```

**Day 04 observed result:**

The workstation's `Test-ComputerSecureChannel -Verbose` command returned `True`.

**Interpretation:** The successful workstation secure-channel test supported normal client-side domain trust functionality within the tested scope.

It provided additional context for the earlier diagnostic discrepancy.

However, the two commands were executed on different computers and tested different execution contexts.

The successful Day 04 result did not establish the cause or resolution of the Day 03 error.

**Limitation:** The precise cause of the earlier `nltest` error 1355 remained unknown.

The workstation result must not be presented as proof that the previous domain controller diagnostic failure was resolved.

## 5. Analyst Assessment

### Observed

- WIN11-CLIENT was configured with IPv4 address `10.10.10.20`.
- The workstation used subnet mask `255.255.255.0`.
- The default gateway was `10.10.10.1`.
- The configured DNS server was `10.10.10.10`.
- DHCP was disabled.
- DNS hostname resolution returned the expected DC01 IPv4 address.
- Domain controller discovery completed successfully.
- The returned domain controller discovery information identified DC01.
- The workstation secure-channel verification returned `True`.
- The previous Day 03 secure-channel verification error remained unexplained.

These observations document the tested client-side domain functionality at the time of the exercise.

### Correlated

The workstation network configuration identified DC01 as the configured DNS server.

The DNS query then confirmed that WIN11-CLIENT could resolve the domain controller hostname to the expected IPv4 address.

Domain controller discovery independently identified DC01 as the domain controller for `corp.local`.

The secure-channel verification subsequently confirmed that the workstation's domain secure channel was healthy at the time of testing.

Together, these results supported successful client-side DNS and domain functionality within the tested scope.

The earlier Day 03 error was considered separately because it occurred on DC01 during a different command.

The successful workstation checks did not establish why the earlier diagnostic command failed.

### Interpretation

The collected findings established a functional client-side baseline for WIN11-CLIENT.

The verified sequence progressed through distinct dependencies:

- Network configuration
- DNS hostname resolution
- Domain controller discovery
- Workstation domain secure-channel verification

Each check provided information that the preceding check did not independently establish.

All four planned workstation checks succeeded.

No workstation domain secure-channel failure was established.

No security incident was established.

#### Hypothesis Assessment

**Hypothesis A: WIN11-CLIENT has an incorrect DNS server configured**

**Evidence against:** The configured DNS server was `10.10.10.10`, matching DC01.

**Assessment:** Not supported by the observed configuration.

**Hypothesis B: WIN11-CLIENT cannot resolve the domain controller hostname**

**Evidence against:** `Resolve-DnsName` returned the expected IPv4 address.

**Assessment:** Not supported by the observed DNS result.

**Hypothesis C: WIN11-CLIENT cannot discover the domain controller**

**Evidence against:** `nltest /dsgetdc:corp.local` completed successfully.

**Assessment:** Not supported by the discovery result.

**Hypothesis D: WIN11-CLIENT has a broken domain secure channel**

**Evidence against:** `Test-ComputerSecureChannel -Verbose` returned `True`.

**Assessment:** Not supported at the time of testing.

**Hypothesis E: The Day 03 secure-channel error has been resolved**

**Evidence limitation:** Day 04 tested a different command on a different computer.

**Assessment:** Cannot be concluded from the collected evidence. The Day 03 diagnostic discrepancy remains unresolved.

### Unknown

The following areas were not fully validated during Day 04:

- Fresh interactive domain user authentication
- Successful Kerberos ticket issuance
- Group Policy processing
- Domain file share access
- Authentication behavior during a simulated failure
- The precise cause of Day 03 secure-channel error 1355
- Complete authentication and authorization functionality

These areas require separate verification before stronger conclusions can be made.

## 6. Actions and Changes

This was a read-only workstation verification exercise.

The completed activities consisted of:

- Inspecting workstation network configuration
- Verifying the configured DNS server
- Resolving the domain controller hostname
- Testing domain controller discovery
- Verifying the workstation domain secure channel
- Comparing results with the Day 03 baseline
- Documenting the findings and limitations

### Execution Context Correction

An initial network configuration check was performed on DC01 instead of WIN11-CLIENT.

The hostname identified the incorrect source system.

The check was repeated on WIN11-CLIENT, and only the workstation result was used to establish the client-side baseline.

This was an investigation process correction, not a network configuration change.

**Configuration changes:** None intentionally performed.

**Secure-channel repair:** Not performed.

**Remediation:** None applied.

**Detection tuning:** None performed.

The successful verification results did not justify modifying the workstation configuration.

## 7. Classification and Disposition

**Classification:** Planned client-side domain functionality verification.

**Disposition:** All four planned workstation verification checks completed successfully.

**Security Conclusion:** No security incident was established.

**Confidence:** High for the specific results directly observed. No conclusion is made about untested authentication or Group Policy functionality.

**Root Cause Investigation:** Not applicable to the successful Day 04 workstation checks.

**Outstanding Historical Finding:** Day 03 secure-channel error 1355 remained unexplained.

The collected evidence supported normal client-side DNS, domain discovery, and secure-channel functionality within the tested scope.

No configuration changes or secure-channel repairs were required based on the results.

The investigation was completed without claiming that the earlier DC01 diagnostic discrepancy had been resolved.

## 8. Verification and Evidence Gaps

### Completed Verification

| Verification Area | Result |
| --- | --- |
| Workstation network configuration | Expected settings observed |
| DNS server configuration | DC01 configured |
| DC01 hostname resolution | Successful |
| Domain controller discovery | Successful |
| Workstation secure channel | Healthy |
| Configuration changes | None intentionally performed |

### Outstanding Verification

The following were not independently verified:

- Fresh interactive domain user authentication
- Successful Kerberos ticket issuance
- Group Policy processing
- Domain file share access
- Authentication behavior during a simulated failure
- The precise cause of Day 03 error 1355

These items are untested or unresolved areas rather than confirmed failures.

The completed tests establish the specific workstation functionality observed during the exercise.

They do not establish complete domain security, authentication coverage, or infrastructure health.

## 9. Handover Notes

An analyst continuing this work should use the documented Day 04 results as the client-side baseline for subsequent Active Directory administration and domain authentication investigations.

### Baseline Reference

| Item | Documented State |
| --- | --- |
| Workstation | WIN11-CLIENT |
| Operating system | Windows 11 |
| Domain | corp.local |
| Workstation IPv4 | 10.10.10.20 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 10.10.10.1 |
| DNS server | 10.10.10.10 |
| Domain controller | DC01 |
| DNS hostname resolution | Successful |
| Domain controller discovery | Successful |
| Workstation secure channel | Healthy |
| Day 03 error 1355 | Unresolved |

### Investigation Considerations

When investigating subsequent workstation authentication or domain connectivity issues, compare current observations against the Day 04 baseline.

Determine:

- Whether the workstation retains the expected network configuration.
- Whether the configured DNS server remains correct.
- Whether the domain controller hostname resolves successfully.
- Whether domain controller discovery succeeds.
- Whether the workstation secure channel remains healthy.
- Whether authentication events or additional diagnostics establish a specific failure.

Do not classify an infrastructure failure based solely on an isolated command result without considering its execution context and supporting evidence.

### Outstanding Verification

Fresh domain authentication, Kerberos ticket issuance, Group Policy processing, file share access, and failure simulation were not independently validated.

The earlier Day 03 diagnostic discrepancy remains documented.

Previously completed Day 04 checks should not be repeated unless a new investigation, configuration change, or troubleshooting requirement makes revalidation necessary.

## 10. Next Action

Proceed to Day 05 | Forests, Domains & Organizational Units under the approved 90 Days of SOC Tier 1 and Tier 2 Mastery roadmap.

Continue within the existing `corp.local` environment.

Use the documented server-side and client-side baseline findings as references for subsequent Active Directory structure and identity administration exercises.

No new infrastructure is required for the approved next exercise.

## Documentation Principle

Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.
