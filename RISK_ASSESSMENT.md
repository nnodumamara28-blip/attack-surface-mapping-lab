# Risk Assessment

Educational rubric from the exercise (not a universal industry standard):

**Risk score = Likelihood x Impact**

| Score | Likelihood | Impact |
|---|---|---|
| 1 | Unlikely under the assessed conditions | Limited operational or security impact |
| 2 | Possible | Moderate impact |
| 3 | Likely | Significant impact |

| Risk score | Priority |
|---|---|
| 1-2 | Low |
| 3-4 | Medium |
| 6 | High |
| 9 | Critical |

A service is not critical just because it uses a well-known port. Justify every score with
evidence and context.


## Summary table
| Finding ID | Observation | Likelihood | Impact | Score | Priority | Recommended action |
|---|---|---|---|---|---|---|
| ASM-01 | Telnet exposed on the LabVM (port 23) | 2 | 3 | 6 | High | Disable Telnet; use SSH |
| ASM-02 | FTP exposed on the LabVM (port 21) | 2 | 2 | 4 | Medium | Disable or replace with SFTP; restrict by firewall |
| ASM-03 | Legacy remote login services on Metasploitable 2 (23, 513, 514) | 2 | 3 | 6 | High | Disable; use SSH |
| ASM-04 | Outdated service versions on Metasploitable 2 | 3 | 3 | 9 | Critical | Keep isolated; upgrade or retire |
| ASM-05 | Database services reachable on Metasploitable 2 (3306, 5432) | 2 | 3 | 6 | High | Restrict to localhost or the application server |
| ASM-06 | Vulnerable training machine shares a subnet with the LabVM | 2 | 2 | 4 | Medium | Move to a separate segment |
| ASM-07 | NTP on UDP 123 on the LabVM, remote reachability unverified | 1 | 1 | 1 | Low | Confirm it is needed; verify with approval |

## Detailed findings

### ASM-01: Telnet exposed on the LabVM
- **Affected asset:** Cybersecurity LabVM, 192.168.56.102
- **Evidence:** Nmap service scan shows 23/tcp open, Linux telnetd (evidence/nmap/); `ss -lntup` shows xinetd listening on *:23 (evidence/local/)
- **Potential threat or consequence:** Telnet sends data unencrypted, so credentials and session content could be read by anyone able to capture traffic on the segment
- **Assumptions and uncertainties:** Authentication settings and host firewall rules were not tested. The lab network is host-only, which limits who can reach it
- **Likelihood and rationale:** 2. Reachable from the whole lab subnet, but the subnet is isolated and small
- **Impact and rationale:** 3. Captured administrator credentials could give control of the host
- **Recommended mitigation:** Disable the Telnet service and use SSH only
- **How to verify the remediation worked:** Rescan port 23 (expect closed) and confirm `ss -lntup` no longer shows :23

### ASM-02: FTP exposed on the LabVM
- **Affected asset:** Cybersecurity LabVM, 192.168.56.102
- **Evidence:** Nmap shows 21/tcp open, vsftpd 2.0.8 or later; `ss -lntup` shows vsftpd on 0.0.0.0:21
- **Potential threat or consequence:** Standard FTP is unencrypted, so credentials and transferred files could be intercepted. Anonymous access could expose files
- **Assumptions and uncertainties:** Nmap could not pin the exact version. Whether anonymous login or encryption is enabled was not tested (testing would be an access attempt)
- **Likelihood and rationale:** 2. Reachable on the subnet, but the need for the service is not established
- **Impact and rationale:** 2. Exposure is limited to the data available through the FTP service
- **Recommended mitigation:** Disable FTP if not needed, or replace it with SFTP over SSH, and restrict access with a firewall
- **How to verify the remediation worked:** Rescan port 21 (expect closed or filtered) and check `ss -lntup`

### ASM-03: Legacy remote login services on Metasploitable 2
- **Affected asset:** Metasploitable 2, 192.168.56.101
- **Evidence:** Nmap shows 23/tcp telnet (Linux telnetd), 513/tcp login (rlogin) and 514/tcp shell (rsh)
- **Potential threat or consequence:** These are old, unencrypted remote-access protocols. Traffic could be read, and rlogin and rsh rely on a weak trust model
- **Assumptions and uncertainties:** Trust configuration and authentication were not tested. The machine is a deliberately vulnerable training target on an isolated network
- **Likelihood and rationale:** 2. Reachable by any host on the lab subnet, but the subnet is isolated
- **Impact and rationale:** 3. These services give remote command-line access to the host
- **Recommended mitigation:** Disable telnet, rlogin and rsh; use SSH instead; restrict by firewall
- **How to verify the remediation worked:** Rescan ports 23, 513 and 514 (expect closed)

### ASM-04: Outdated service versions on Metasploitable 2
- **Affected asset:** Metasploitable 2, 192.168.56.101
- **Evidence:** Nmap -sV reported vsftpd 2.3.4, OpenSSH 4.7p1, Apache httpd 2.2.8, ISC BIND 9.4.2, MySQL 5.0.51a, PostgreSQL 8.3.0 to 8.3.7, and ProFTPD 1.3.1 (evidence/nmap/)
- **Potential threat or consequence:** Old releases may contain publicly documented vulnerabilities. For example, vsftpd 2.3.4 is publicly documented as having been distributed with a backdoor in a compromised download
- **Assumptions and uncertainties:** This is version-based only. Nothing was tested, so no vulnerability is confirmed on this host. Nmap banners can be inaccurate. Support status must be confirmed on vendor end-of-life pages and nvd.nist.gov, and cited
- **Likelihood and rationale:** 3. Old versions with public vulnerability records, reachable from every host on the subnet. If the support status cannot be confirmed, lower this to 2
- **Impact and rationale:** 3. A successful attack on these services could mean full system compromise
- **Recommended mitigation:** On a real system, upgrade to supported versions or retire the machine. For this training machine, keep it isolated and never connect it to a production network
- **How to verify the remediation worked:** Rerun the service scan to confirm new versions and check them against vendor advisories

### ASM-05: Database services reachable on Metasploitable 2
- **Affected asset:** Metasploitable 2, 192.168.56.101
- **Evidence:** Nmap shows 3306/tcp MySQL 5.0.51a and 5432/tcp PostgreSQL 8.3.0 to 8.3.7 open
- **Potential threat or consequence:** Any host on the segment can attempt to connect directly to the databases, and weak authentication could expose data
- **Assumptions and uncertainties:** Authentication strength and the data stored were not tested
- **Likelihood and rationale:** 2. Reachable on the subnet, but the subnet is isolated
- **Impact and rationale:** 3. Databases normally hold the most valuable data
- **Recommended mitigation:** Bind databases to localhost or the application server only, and restrict by firewall
- **How to verify the remediation worked:** Rescan ports 3306 and 5432 from Kali (expect closed or filtered)

### ASM-06: Vulnerable training machine shares a subnet with the LabVM
- **Affected asset:** 192.168.56.101 and 192.168.56.102
- **Evidence:** Host discovery places both machines in 192.168.56.0/24 (evidence/nmap/)
- **Potential threat or consequence (hypothesis):** If the training machine were compromised, it could be used as a foothold to reach the LabVM
- **Assumptions and uncertainties:** Not tested. The subnet is host-only, which limits the exposure
- **Likelihood and rationale:** 2. Possible if the training machine is attacked
- **Impact and rationale:** 2. Limited to the lab environment
- **Recommended mitigation:** Place vulnerable training machines on a separate network segment, with firewall rules between them and other systems
- **How to verify the remediation worked:** From the LabVM, confirm the training machine's ports are unreachable (authorized check)

### ASM-07: NTP service on the LabVM, remote reachability unverified
- **Affected asset:** Cybersecurity LabVM, 192.168.56.102
- **Evidence:** `ss -lntup` shows ntpd on UDP 123 bound to 0.0.0.0 and 192.168.56.102
- **Potential threat or consequence:** An unneeded or misconfigured time service could be misused
- **Assumptions and uncertainties:** UDP was outside the approved scope, so remote reachability is **unverified**. The score is provisional
- **Likelihood and rationale:** 1. No evidence of remote exposure
- **Impact and rationale:** 1. Limited impact
- **Recommended mitigation:** Confirm whether NTP is needed and restrict it to required interfaces
- **How to verify the remediation worked:** With instructor approval, run a limited UDP check; or review the ntpd configuration

