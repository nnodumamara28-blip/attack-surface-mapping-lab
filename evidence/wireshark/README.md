# evidence/wireshark/

## What the capture reveals

I captured traffic on the vboxnet0 interface (the host-only lab network, 192.168.56.0/24) in Wireshark using the display filter arp || icmp || tcp. While capturing, I ran an approved host discovery scan (nmap -sn), a ping test, and a TCP connect scan of 192.168.56.102. The capture is saved as attack-surface-discovery.pcapng.

Host discovery (ARP): Because the targets are on the same local network as Kali, the discovery scan used ARP instead of ICMP. Kali (192.168.56.1) broadcast an ARP request for each address in the subnet, asking who owns it. Only the live hosts, 192.168.56.101 and 192.168.56.102, sent ARP replies. Addresses that did not reply were treated as offline.

ICMP: The ping test showed four ICMP echo requests from 192.168.56.1 to 192.168.56.102 and four echo replies, confirming that the host answers ping. A ping reply shows a host is reachable, but a missing reply would not prove a host is down, because ICMP can be filtered.

Port scanning (TCP): The connect scan produced many TCP packets from 192.168.56.1 to 192.168.56.102, each with the SYN flag set and aimed at a different destination port. Open ports (21, 22, and 23) answered with SYN/ACK and the connection was then completed and closed. Closed ports answered with a reset (RST), refusing the connection. The source port was a high temporary port chosen by Kali.

What this means for a defender: Discovery and scanning are visible on the network. One source sending connection attempts to many different ports in a very short time is a recognizable scanning pattern that monitoring tools and analysts can detect.

Limitations: What is visible depends on the VirtualBox network mode and the interface being monitored. This capture covers one scan of one target. No traffic from outside the approved lab network was captured.
