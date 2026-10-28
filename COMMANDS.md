# Commands Used

Run only against the instructor-approved scope in [SCOPE.md](SCOPE.md). Replace every
placeholder in angle brackets with your real value (without the brackets).

Save outputs with dated names, for example `2026-10-15_<target-ip>_nmap-top100.txt`.
Files created with `sudo` belong to root, so fix ownership before committing:
```
sudo chown -R "$USER":"$(id -gn)" evidence
```

## Task 2: Workstation network configuration (on Kali)
| Command | Purpose |
|---|---|
| `ip -br addr` | Brief list of interfaces and their IP addresses. Identifies the lab-facing interface |
| `ip route` | Shows routes and the default gateway, so you know where traffic to the lab goes |
| `ping -c 4 <target-ip>` | Four ICMP echo requests to test basic reachability. No reply does not prove the host is down |

Save: `ip -br addr | tee evidence/local/YYYY-MM-DD_kali_ip-br-addr.txt`

## Task 3: Host discovery (on Kali)
| Command | Purpose |
|---|---|
| `sudo nmap -sn <authorized-subnet> -oN evidence/nmap/YYYY-MM-DD_host-discovery.txt` | `-sn` finds live hosts without a port scan. `-oN` saves the output |

## Task 4: Ports and services (on Kali)
| Command | Purpose |
|---|---|
| `sudo nmap -sT --top-ports 100 <target-ip> -oN evidence/nmap/YYYY-MM-DD_<target-ip>_top100.txt` | TCP connect scan of the 100 most common ports |
| `sudo nmap -sV -p <open-ports> <target-ip> -oN evidence/nmap/YYYY-MM-DD_<target-ip>_services.txt` | Identifies the service and version on the open ports found above (comma-separated list) |

Do not add exploitation scripts or intrusive options.

## Task 5: Local listening services (on each authorized target)
| Command | Purpose |
|---|---|
| `sudo ss -lntup` | Lists listening TCP and UDP sockets with processes, to compare with the remote scan |
| `sudo systemctl status ssh` | Shows whether the SSH service is installed, enabled, and running |
| `curl -I http://<target-ip>/` | Fetches HTTP response headers only (authorized web service only) |

Save: `sudo ss -lntup | tee ss-lntup.txt`, then copy the file into `evidence/local/` with a dated name.

## Task 9: Wireshark (on Kali)
1. Open Wireshark and select the interface connected to the lab network.
2. Apply the display filter: `arp || icmp || tcp`
3. Start the capture, run one approved discovery scan or connectivity test, then stop the capture.
4. Save as `evidence/wireshark/attack-surface-discovery.pcapng`

## Git (submission)
```
git init
git add .
git commit -m "Add attack surface mapping assessment"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/attack-surface-mapping-lab.git
git push -u origin main
```
