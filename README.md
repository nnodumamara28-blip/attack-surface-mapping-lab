
# Attack Surface Mapping Lab

Authorized attack surface mapping and exposure assessment of a simulated organization,
**Eselsa Technologies**, performed in an approved VirtualBox lab as part of my
Cybersecurity Analyst / Ethical Hacking and Penetration Testing training.

> This is a mapping exercise, not an exploitation exercise. Nothing was exploited,
> no authentication was bypassed, and no service was disrupted.

## Learning objectives
1. Explain attack surface, attack vector, asset inventory, and exposure
2. Identify assets within an explicitly authorized scope
3. Discover live hosts with appropriate techniques
4. Identify open ports and running services
5. Distinguish expected services from unnecessary exposure
6. Identify potential attack paths from documented evidence
7. Assess risk using likelihood and impact
8. Recommend practical mitigations
9. Produce a professional attack surface map and report
10. Document the work on GitHub with evidence

## Authorized lab topology
| Role | Machine | IP address | Network |
|---|---|---|---|
| Assessment workstation | Kali Linux | 192.168.56.1 | vboxnet0 (VirtualBox host-only) |
| Target 1 | Cybersecurity LabVM (Ubuntu-based Linux training workstation) | 192.168.56.102 | host-only |
| Target 2 | Metasploitable 2 | 192.168.56.101 | host-only |

Authorized subnet: 192.168.56.0/24. The scope was approved by my instructor on 21 October 2026
(details in [SCOPE.md](SCOPE.md)). The Wi-Fi network connected to the workstation was excluded
and never scanned.

## Tools used and why
| Tool | Purpose in this exercise |
|---|---|
| `ip` | Show the workstation's interfaces, addresses, and routes, so the scan range is chosen on purpose |
| `ping` | Basic connectivity test (a failed ping alone proves nothing) |
| `nmap` | Host discovery, TCP port scanning, and service/version identification |
| `ss` | Show which services are listening on a machine and what addresses they are bound to |
| `curl` | Inspect HTTP response headers of an authorized web service |
| Wireshark | Observe what discovery and scanning look like on the wire |
| Markdown / Mermaid | Documentation and the attack surface diagram |
| Git and GitHub | Version-controlled, reviewable submission |

## High-level methodology
1. Define scope and record authorization ([SCOPE.md](SCOPE.md))
2. Record the workstation's network configuration
3. Discover live hosts in the approved subnet only
4. Scan TCP ports, then identify services on the open ports
5. Compare remote results with locally listening sockets
6. Classify findings into network, application, host/identity, and configuration surface
7. Score and prioritize risk (likelihood x impact)
8. Recommend and plan mitigations
9. Capture discovery traffic in Wireshark
10. Write the report, with observed facts separated from hypotheses

Every command is documented with its purpose in [COMMANDS.md](COMMANDS.md).

## Key findings
Findings are exposures and risks supported by saved evidence. No vulnerability was tested or confirmed.
- **Metasploitable 2 exposes 18 open TCP ports**, and Nmap reported old versions (for example vsftpd 2.3.4,
  OpenSSH 4.7p1 and Apache httpd 2.2.8). These are version-based observations only.
- **Unencrypted remote login services** are exposed: Telnet on both targets, plus rlogin and rsh
  (ports 513 and 514) on Metasploitable 2.
- **The LabVM runs FTP (21), SSH (22) and Telnet (23)**, all listening on every interface. The local `ss`
  review matched the remote scan for these three services.
- **Database services** (MySQL 3306 and PostgreSQL 5432) are reachable on Metasploitable 2.
- **Observed control:** X11 (6000) on Metasploitable 2 answered "access denied".
- **Unverified:** UDP services on the LabVM (NTP on 123, DHCP client on 68), because UDP was outside the
  approved scope.
- **Wireshark** showed the discovery scan as ARP requests and the port scan as many TCP connection
  attempts from the workstation to different ports on the target.

## Risk summary
| Priority | Number of findings | Finding IDs |
|---|---|---|
| Critical (9) | 1 | ASM-04 |
| High (6) | 3 | ASM-01, ASM-03, ASM-05 |
| Medium (3-4) | 2 | ASM-02, ASM-06 |
| Low (1-2) | 1 | ASM-07 |

The scoring uses the exercise's simplified likelihood x impact rubric. See [RISK_ASSESSMENT.md](RISK_ASSESSMENT.md).

## Remediation summary
- Keep Metasploitable 2 isolated on the host-only network, and upgrade or retire outdated software on any real system
- Disable Telnet on the LabVM, and disable telnet, rlogin and rsh on Metasploitable 2 in favour of SSH
- Restrict the database services to localhost or the application server
- Disable or replace FTP, and separate vulnerable training machines from other systems
- Confirm whether NTP is needed
- Repeat the approved scans after any change to verify the exposure is gone

The full plan, with responsible roles and verification methods, is in [REMEDIATION_PLAN.md](REMEDIATION_PLAN.md).

## Documents
- [Scope and authorization](SCOPE.md)
- [Asset inventory](ASSET_INVENTORY.md)
- [Service inventory and local listening review](SERVICE_INVENTORY.md)
- [Risk assessment](RISK_ASSESSMENT.md)
- [Remediation plan](REMEDIATION_PLAN.md)
- [Final report](REPORT.md)
- [Commands used](COMMANDS.md)
- [Questions and answers](QUESTIONS.md)
- [Attack surface diagram](diagrams/)
- [Evidence](evidence/)

## Scope statement
- [x] All testing was performed within the instructor-approved lab scope, on systems I own or was explicitly authorized to test.

## Repository structure
```
attack-surface-mapping-lab/
├── README.md
├── SCOPE.md
├── ASSET_INVENTORY.md
├── SERVICE_INVENTORY.md
├── RISK_ASSESSMENT.md
├── REMEDIATION_PLAN.md
├── REPORT.md
├── COMMANDS.md
├── QUESTIONS.md
├── diagrams/
│   ├── attack-surface-map.mmd
│   └── attack-surface-map.png
├── evidence/
│   ├── screenshots/
│   ├── nmap/
│   ├── wireshark/
│   ├── local/
│   └── .gitkeep
└── .gitignore
```
