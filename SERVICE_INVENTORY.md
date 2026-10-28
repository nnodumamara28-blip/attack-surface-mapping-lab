


| Cybersecurity LabVM (192.168.56.102) | 0.0.0.0:21                                       | TCP         | vsftpd                        | Not established              | Not established         | Yes, port 21 open                          |
| Cybersecurity LabVM (192.168.56.102) | 0.0.0.0:22 and [::]:22                           | TCP         | sshd                          | Yes (remote administration)  | Yes, for administrators | Yes, port 22 open                          |
| Cybersecurity LabVM (192.168.56.102) | *:23                                             | TCP         | xinetd (telnet)               | Not established              | Not established         | Yes, port 23 open                          |
| Cybersecurity LabVM (192.168.56.102) | 127.0.0.53:53                                    | TCP and UDP | systemd-resolved              | Yes (local DNS stub)         | No                      | No, loopback only                          |
| Cybersecurity LabVM (192.168.56.102) | 0.0.0.0:123 plus specific and loopback addresses | UDP         | ntpd                          | Probably (time sync), verify | Not established         | Not scanned (UDP out of scope), unverified |
| Cybersecurity LabVM (192.168.56.102) | 192.168.56.102:68                                | UDP         | systemd-network (DHCP client) | Yes                          | No                      | Not scanned                                |


 The local listening-socket review of the Cybersecurity LabVM (192.168.56.102) matched the remote Nmap scan for three services: FTP on port 21, SSH on port 22, and Telnet on port 23 (started by the xinetd service).
 All three listen on every network interface (0.0.0.0, [::] or *), which is consistent with Kali being able to reach them across the lab subnet.
 The DNS service from systemd-resolved on 127.0.0.53:53 did not appear in the remote scan because it listens only on the loopback address, so it can be reached only from the machine itself.
 The NTP service on UDP port 123 and the DHCP client on UDP port 68 could not be compared because UDP scanning was outside the approved scope,
 so whether they are reachable remotely is unverified.
 Using both views together is useful because the local view shows everything a machine is listening for, while the remote view shows only what an outsider can actually reach.
