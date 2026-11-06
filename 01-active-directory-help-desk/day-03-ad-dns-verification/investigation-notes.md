
# Day 03 | Active Directory Domain Services and DNS Verification Investigation Notes

## Master Station Log

| Field | Details |
| --- | --- |
| Case ID | SOC-AD-D03-001 |
| Exercise | Active Directory Domain Services and DNS Verification |
| Date | 2026-11-05 |
| Environment | Windows Server 2022 |
| Domain Controller | DC01 |
| Domain | corp.local |
| IP Address | 10.10.10.10 |
| Phase | 01 - Active Directory & Help Desk |
| Activity Type | Planned infrastructure verification |
| Status | Verification completed, one diagnostic discrepancy unresolved |
| Disposition | Core service and DNS checks completed. Secure-channel error 1355 remains unresolved. |

## 1. Initial Objective

Verify that the domain controller's core Active Directory and DNS services were operational within the scope of the planned exercise.

The verification focused on:

- Active Directory Domain Services availability
- DNS service availability
- DNS client configuration
- Hostname resolution
- LDAP service location records
- Domain controller discovery
- Netlogon service status
- Active Directory Advertising diagnostics
- DNS diagnostics
- Kerberos Key Distribution Center service status

This was a planned infrastructure verification exercise, not a response to a security alert.

During testing, the secure-channel verification command returned an unexpected error:

`I_NetLogonControl failed: Status = 1355 0x54b ERROR_NO_SUCH_DOMAIN`

The investigation therefore included additional read-only diagnostics to assess the significance of the error without modifying the domain controller configuration.

## 2. Systems and Evidence Sources

- DC01 running Windows Server 2022
- Active Directory domain: corp.local
- Domain controller IPv4 address: 10.10.10.10
- Administrative Windows PowerShell
- Windows service status queries
- DNS client configuration
- DNS A, AAAA, and SRV record queries
- `nltest` domain controller discovery and secure-channel verification
- `dcdiag` Advertising and DNS diagnostics

The investigation was performed on DC01.

The diagnostic sequence reflected the actual order of testing. Exact execution timestamps were not recorded for every command.

No Windows Security authentication events were retrieved as part of the documented exercise.

## 3. Actions and Verified Results

| Action | Verified Result |
| --- | --- |
| Check NTDS and DNS services | Both Running, Automatic |
| Inspect DNS client configuration | Ethernet DNS set to 127.0.0.1 |
| Resolve `dc01.corp.local` | Expected A record returned |
| Query LDAP SRV record | Expected DC01 target returned |
| Run domain controller discovery | Successful |
| Run secure-channel verification | Failed with error 1355 |
| Check Netlogon service | Running, Automatic |
| Run Advertising diagnostic | Passed |
| Run DNS diagnostic | Passed |
| Check Kerberos KDC service | Running, Automatic |

The results established the observed status of the tested services and domain discovery functions.

One diagnostic discrepancy remained unresolved.

The successful checks did not independently establish complete Active Directory health or explain the secure-channel verification failure.

## 4. Technical Evidence and Findings

### Finding 01: Active Directory and DNS Service Status

**Verification method:** Administrative PowerShell service query.

**Command executed:**

`Get-Service NTDS,DNS | Select-Object Name,Status,StartType`

**Observed:**

- DNS: Running, Automatic
- NTDS: Running, Automatic

**Interpretation:** Both core services were active at the time of inspection.

**Limitation:** Service status alone does not establish complete application functionality, successful authentication, or the health of all dependent services.

### Finding 02: DNS Client Configuration

**Verification method:** PowerShell DNS client configuration query.

**Command executed:**

`Get-DnsClientServerAddress -AddressFamily IPv4`

**Observed:**

- Ethernet DNS server: `127.0.0.1`
- Loopback Pseudo-Interface 1: No DNS server listed

**Interpretation:** DC01 was configured to use its locally hosted DNS service through the IPv4 loopback address.

**Limitation:** DNS client configuration alone does not establish successful resolution or the availability of DNS services to other domain members.

### Finding 03: Domain Controller Hostname Resolution

**Verification method:** PowerShell DNS resolution query.

**Command executed:**

`Resolve-DnsName dc01.corp.local`

**Observed:**

- A record: `10.10.10.10`
- AAAA record: `fe80::b4f9:f7e3:2e86:4435`

**Interpretation:** DNS resolution returned the expected IPv4 address for DC01.

The result supported the availability of the tested hostname resolution path.

**Limitation:** Successful local resolution does not independently establish remote client connectivity, authentication functionality, or complete DNS health.

### Finding 04: LDAP SRV Record Resolution

**Verification method:** DNS SRV record query.

**Command executed:**

`Resolve-DnsName _ldap._tcp.dc._msdcs.corp.local -Type SRV`

**Observed:**

| Attribute | Recorded Value |
| --- | --- |
| Target | dc01.corp.local |
| Port | 389 |
| Priority | 0 |
| Weight | 100 |
| Target IPv4 | 10.10.10.10 |

**Interpretation:** The DNS service location record used for LDAP domain controller discovery was present and returned the expected DC01 target.

This provided supporting evidence that the tested domain controller discovery records were available.

**Limitation:** SRV record resolution does not establish successful LDAP binding, authentication, or directory query authorization.

### Finding 05: Domain Controller Discovery

**Verification method:** `nltest` domain discovery command.

**Command executed:**

`nltest /dsgetdc:corp.local`

**Observed:**

- Domain controller: `dc01.corp.local`
- Address: `10.10.10.10`
- Domain: `corp.local`
- Forest: `corp.local`
- Site: `Default-First-Site-Name`
- Command completed successfully

**Interpretation:** Domain controller discovery succeeded from the tested execution context.

The result was consistent with the successful DNS hostname and LDAP SRV record queries.

**Limitation:** Successful domain controller discovery does not independently establish successful user authentication, workstation trust health, or complete domain availability.

### Finding 06: Secure-Channel Verification Failure

**Verification method:** `nltest` secure-channel verification.

**Command executed:**

`nltest /sc_verify:corp.local`

**Observed output:**

`I_NetLogonControl failed: Status = 1355 0x54b ERROR_NO_SUCH_DOMAIN`

The command returned error 1355 when executed on DC01.

**Interpretation:** The secure-channel verification command failed in the tested domain controller execution context.

The result represented an unexpected diagnostic discrepancy requiring interpretation alongside other findings.

The error alone was insufficient to conclude that the domain was unavailable or that a Windows workstation trust relationship was broken.

**Limitation:** The exact cause of error 1355 was not established.

No successful resolution of this error was demonstrated.

**Investigation decision:** Continue read-only diagnostics and compare the result with independent service, DNS, discovery, and domain controller health checks before considering configuration changes.

### Finding 07: Netlogon Service Status

**Verification method:** PowerShell service query.

**Command executed:**

`Get-Service Netlogon | Select-Object Name,Status,StartType`

**Observed:**

- Netlogon: Running, Automatic

**Interpretation:** The Netlogon service was running at the time of inspection.

This finding did not support the hypothesis that the service was stopped.

**Limitation:** A running Netlogon service does not independently establish successful secure-channel verification or identify the cause of error 1355.

### Finding 08: Domain Controller Advertising Diagnostics

**Verification method:** Targeted Active Directory diagnostic.

**Command executed:**

`dcdiag /test:Advertising`

**Observed output:**

`DC01 passed test Connectivity`

`DC01 passed test Advertising`

**Interpretation:** DC01 passed the tested connectivity and Advertising checks.

These results did not support the hypothesis that the domain controller was failing the tested Advertising diagnostic.

**Limitation:** The successful diagnostic did not explain the secure-channel verification error or establish that every domain controller function was healthy.

### Finding 09: DNS Diagnostics

**Verification method:** Targeted Active Directory DNS diagnostic.

**Command executed:**

`dcdiag /test:DNS`

**Observed output:**

`DC01 passed test Connectivity`

`DC01 passed test DNS`

`corp.local passed test DNS`

**Interpretation:** The targeted DNS diagnostic completed successfully.

The result supported the earlier successful hostname resolution, LDAP SRV record resolution, and domain controller discovery observations.

**Limitation:** The diagnostic did not cover every possible client-side DNS scenario, authentication path, or network connectivity condition.

It also did not establish the cause of secure-channel error 1355.

### Finding 10: Kerberos KDC Service Status

**Verification method:** PowerShell service query.

**Command executed:**

`Get-Service Kdc | Select-Object Name,Status,StartType`

**Observed:**

- KDC: Running, Automatic

**Interpretation:** The Kerberos Key Distribution Center service was active at the time of inspection.

**Limitation:** Service status alone did not establish successful Kerberos ticket issuance, ticket validation, or user authentication.

## 5. Analyst Assessment

### Observed

- NTDS and DNS services were Running.
- DC01 used `127.0.0.1` as its configured Ethernet IPv4 DNS server.
- The DC01 hostname resolved to the expected IPv4 address.
- An IPv6 link-local AAAA record was also returned.
- The LDAP SRV record identified DC01 on port 389.
- Domain controller discovery completed successfully.
- Secure-channel verification returned error 1355.
- Netlogon was Running.
- The Advertising diagnostic passed.
- The DNS diagnostic passed.
- The Kerberos KDC service was Running.

The secure-channel verification failure was the primary unexpected finding.

### Correlated

The secure-channel verification returned error 1355.

However, several independent checks produced successful results:

- Core Active Directory and DNS services were running.
- Hostname resolution returned the expected address.
- The LDAP SRV record was present.
- Domain controller discovery succeeded.
- Netlogon was running.
- Targeted connectivity and Advertising diagnostics passed.
- Targeted DNS diagnostics passed.
- The Kerberos KDC service was running.

These observations supported the operational status of the tested domain services.

They did not conclusively explain or resolve the secure-channel verification error.

The diagnostic results therefore required separate interpretation rather than treating one failed command as definitive evidence of a domain-wide failure.

### Interpretation

The collected findings supported the availability of the core services and discovery functions tested during Day 03.

The successful DNS queries and domain discovery results did not support a conclusion that the domain was generally undiscoverable in the tested context.

The successful Advertising and DNS diagnostics provided additional evidence against a broad failure of the tested domain controller functions.

However, the secure-channel verification command failed.

Because the exact cause was not established, the result remained an unresolved diagnostic discrepancy.

No security incident was established.

#### Hypothesis Assessment

**Hypothesis A: Core DNS functionality is unavailable**

**Evidence against:** Hostname resolution, LDAP SRV resolution, domain controller discovery, and targeted DNS diagnostics succeeded.

**Assessment:** Not supported by the collected evidence.

**Hypothesis B: The Netlogon service is stopped**

**Evidence against:** Netlogon reported a Running status.

**Assessment:** Not supported at the time of inspection.

**Hypothesis C: DC01 is not advertising as a domain controller**

**Evidence against:** The Advertising diagnostic passed.

**Assessment:** Not supported by the collected evidence.

**Hypothesis D: The secure-channel error is related to command context or another untested condition**

**Supporting observation:** The command failed on DC01 despite successful domain controller discovery and targeted diagnostics.

**Assessment:** Possible but unconfirmed. The root cause remains unknown.

### Unknown

The following areas were not fully validated during Day 03:

- The precise cause of secure-channel error 1355
- Whether the same command succeeds on the Windows 11 domain member
- Whether a test user can complete fresh domain authentication
- Whether Kerberos ticket issuance succeeds
- Whether Group Policy processing succeeds
- Complete Active Directory replication health, if applicable
- Whether additional diagnostic context would explain the observed discrepancy

These areas require separate verification before stronger conclusions can be made.

## 6. Actions and Changes

This was a read-only infrastructure verification exercise.

The completed activities consisted of:

- Querying NTDS and DNS service status
- Inspecting DNS client configuration
- Resolving DNS A and AAAA records
- Resolving the LDAP SRV record
- Testing domain controller discovery
- Executing secure-channel verification
- Recording error 1355
- Checking the Netlogon service
- Running targeted Advertising diagnostics
- Running targeted DNS diagnostics
- Checking the Kerberos KDC service
- Comparing the results across independent checks

**Configuration changes:** None intentionally performed.

**Service restarts:** None intentionally performed.

**Remediation:** None applied.

**Detection tuning:** None performed.

The observed diagnostic discrepancy was documented rather than treated as justification for an unverified configuration change.

## 7. Classification and Disposition

**Classification:** Planned Active Directory infrastructure verification.

**Unexpected Finding:** Secure-channel verification error 1355.

**Disposition:** Planned verification completed with an unresolved diagnostic discrepancy documented for follow-up.

**Security Conclusion:** No security incident was established.

**Root Cause:** Not determined.

The successful service, DNS, discovery, and targeted diagnostic checks supported the operational status of the tested components.

The secure-channel verification failure remained unresolved.

No configuration changes were justified solely by the evidence collected.

The investigation was closed for the planned Day 03 scope without claiming that error 1355 was fixed.

## 8. Verification and Evidence Gaps

The following results were established during the exercise:

| Verification Area | Result |
| --- | --- |
| AD DS service status | Running |
| DNS service status | Running |
| DNS client configuration | Recorded |
| Hostname resolution | Successful |
| LDAP SRV record resolution | Successful |
| Domain controller discovery | Successful |
| Netlogon service status | Running |
| Advertising diagnostic | Passed |
| DNS diagnostic | Passed |
| Kerberos KDC service status | Running |
| Secure-channel verification | Failed, unresolved |

The investigation did not establish:

- The precise cause of error 1355
- Successful secure-channel verification from a Windows 11 domain member
- Fresh domain user authentication
- Successful Kerberos ticket issuance
- Successful Group Policy processing
- Complete Active Directory replication health

These items are not recorded as confirmed infrastructure failures.

They represent untested or unresolved areas that require separate verification.

No broader domain failure or successful remediation should be inferred from the available results.

## 9. Handover Notes

An analyst continuing this work should use the documented Day 03 results as the starting reference for any subsequent domain service or secure-channel investigation.

### Baseline Reference

| Item | Documented State |
| --- | --- |
| Domain controller | DC01 |
| Operating system | Windows Server 2022 |
| Domain | corp.local |
| IPv4 address | 10.10.10.10 |
| NTDS service | Running |
| DNS service | Running |
| Netlogon service | Running |
| Kerberos KDC service | Running |
| Hostname resolution | Successful |
| LDAP SRV resolution | Successful |
| Domain controller discovery | Successful |
| Advertising diagnostic | Passed |
| DNS diagnostic | Passed |
| Secure-channel verification | Error 1355, unresolved |

### Investigation Considerations

The next analyst should recognize that the unexpected command result occurred on DC01 itself.

The successful domain controller discovery, DNS resolution, and targeted diagnostic results must be considered alongside the failed secure-channel verification.

Do not assume that the domain is broken solely because `nltest /sc_verify:corp.local` returned error 1355.

Do not treat the successful diagnostics as proof that the error has been resolved.

The precise cause remains unknown.

### Outstanding Verification

When the Windows 11 domain member is available during an appropriate future exercise:

- Verify its domain secure-channel status.
- Compare the results with the DC01 observation.
- Record the exact command output.
- Record the execution context.
- Record the execution timestamp.
- Assess whether additional evidence explains the discrepancy.

These activities are follow-up recommendations and were not completed during Day 03.

Avoid modifying the DC01 configuration solely to address the isolated command result without additional supporting evidence.

Previously completed Day 03 checks should not be repeated unless a new investigation, configuration change, or troubleshooting requirement makes revalidation necessary.

## 10. Next Action

Continue with Day 04 under the approved 90 Days of SOC Tier 1 and Tier 2 Mastery roadmap.

Preserve the unresolved secure-channel error as a documented finding.

Any additional validation should occur within an approved future exercise and must not be represented as completed Day 03 work.

## Documentation Principle

Record what was observed, distinguish interpretation from evidence, identify unknowns, and leave enough information for another analyst to continue the work.
