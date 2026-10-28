
# Asset Inventory

Only facts supported by saved evidence are recorded. Roles are not decided from IP addresses
alone.

## 1. Assessment workstation network configuration (Task 2)

| Item | Observed value | Evidence |
|---|---|---|
| Interface connected to the lab | vboxnet0 (VirtualBox host-only network) | evidence/local/ (ip output) and screenshots |
| Workstation IP address in the lab | 192.168.56.1 | evidence/local/ (ip output) |
| Network prefix / subnet | /24 (255.255.255.0), subnet 192.168.56.0/24 | evidence/local/ (ip output) |
| Default gateway on the lab interface | None. The lab is a directly connected, isolated segment. The workstation's default gateway belongs to its Wi-Fi interface (wlan0), which is excluded from scope. | evidence/local/ (ip route output) |

### Explanation
The workstation has two network interfaces: vboxnet0 and wlan0. Only vboxnet0 is connected to the
lab, so every scan was directed at the 192.168.56.0/24 subnet through that interface. The wlan0 interface
connects to my Wi-Fi network and was never scanned. Pinging an authorized target confirms basic
reachability, but a failed ping would not prove the host is offline, because ICMP can be filtered.

## 2. Host discovery results (Task 3)

Command: `sudo nmap -sn 192.168.56.0/24 -e vboxnet0` (output in evidence/nmap/)

| IP address | Hostname | Observed status | Probable role | Evidence | Verified? |
|---|---|---|---|---|---|
| 192.168.56.1 | kali | Online (the assessment workstation itself) | Security assessment workstation (Kali Linux) | Local interface vboxnet0 from `ip -br addr`; terminal prompt | Yes |
| 192.168.56.101 | metasploitable | Online | Deliberately vulnerable training target (Metasploitable 2) | Nmap MAC address 08:00:27:2A:E5:4D (Oracle VirtualBox virtual NIC), which matches the HWaddr shown by `ifconfig` on the machine's console | Yes |
| 192.168.56.102 | labvm | Online | Linux training workstation (Cybersecurity LabVM, Ubuntu-based) | Nmap MAC address 08:00:27:55:44:07 (Oracle VirtualBox virtual NIC), which matches the address shown by `ip address` on the machine's console | Yes |

## 3. How roles were established
- **192.168.56.1** is my own workstation, confirmed from its local interface.
- **192.168.56.101** and **192.168.56.102** are virtual machines I imported into my own VirtualBox. Their MAC addresses in the Nmap output match the addresses shown on each machine's own console, which links each IP to the right machine.
- Nmap's "Service Info: Host" field comes from service banners and was not used as a hostname. Hostnames come from each machine's own console or system log.
- The LabVM is a desktop-style Linux training workstation, so it is not described as a server.
