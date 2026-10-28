
# Remediation Plan

Recommendations are based only on the evidence in this repository. No changes were made to any
system during this assessment. Any change to a running service would need authorization first,
and business-critical services must not be disabled without it.

## Prioritized plan (highest risk first)
| Finding ID | Proposed remediation | Priority | Responsible role | Verification method |
|---|---|---|---|---|
| ASM-04 | Keep Metasploitable 2 isolated on the host-only network and never connect it to a production network. On a real system, upgrade to supported versions or retire the machine. | Critical (9) | Security team and system administrator | Rerun the approved service scan and compare versions with vendor advisories |
| ASM-01 | Disable the Telnet service on the LabVM and use SSH for remote administration. | High (6) | System administrator | Rescan port 23 (expect closed); confirm `ss -lntup` no longer shows :23 |
| ASM-03 | Disable telnet, rlogin and rsh on Metasploitable 2; use SSH only; restrict access with a firewall. | High (6) | System administrator | Rescan ports 23, 513 and 514 (expect closed) |
| ASM-05 | Bind MySQL and PostgreSQL to localhost or the application server only, and restrict by firewall. | High (6) | System administrator | Rescan ports 3306 and 5432 from Kali (expect closed or filtered) |
| ASM-02 | Disable FTP on the LabVM if it is not needed, or replace it with SFTP over SSH, and restrict by firewall. | Medium (4) | System administrator | Rescan port 21 (expect closed or filtered); check `ss -lntup` |
| ASM-06 | Place vulnerable training machines on a separate network segment with firewall rules between segments. | Medium (4) | Network administrator and security team | From the LabVM, confirm the training machine's ports are unreachable (authorized check) |
| ASM-07 | Confirm whether NTP is needed and restrict it to the required interfaces. | Low (1) | System administrator | Review the ntpd configuration; with instructor approval, run a limited UDP check |

## Suggested order of work
Start with ASM-04. It has the highest score, and the first protective step costs nothing: keeping Metasploitable 2 isolated on the host-only network. The next priority is the High findings, beginning with ASM-01 because disabling Telnet is quick and removes cleartext credentials from the LabVM. ASM-03 and ASM-05 follow. They also carry a High score and need only configuration changes on the training machine. The Medium findings (ASM-02 and ASM-06) come next, and the Low finding (ASM-07) last, since its exposure is unverified. After any change, the approved scans should be repeated to confirm the exposure is gone, and the assessment should be repeated after significant infrastructure changes so new exposure is not missed.

## Controls from the exercise guidance used here
Disable unneeded services; restrict remote administration; restrict inbound access with firewalls; segment vulnerable systems from other systems; keep an accurate asset inventory and repeat assessments after significant changes.
