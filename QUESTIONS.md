


# ATTACK SURFACE MAPPING AND EXPOSURE ASSESSMENT
## Answers to Practical Tasks and Final Questions


---

# TASK 1: ESTABLISH THE ASSESSMENT SCOPE

### 1. Why must an assessment have a clearly defined scope?

A clearly defined scope identifies the systems and networks that the analyst is authorized to assess. It helps the analyst understand what is included and excluded from the exercise. It also prevents accidental testing of systems that do not belong to the lab.

### 2. What could happen if a student scans the wrong subnet?

Scanning the wrong subnet could affect systems that are not part of the exercise. It could generate unwanted network traffic, trigger security alerts, violate organizational policies, or result in unauthorized testing. Therefore, the analyst must verify the correct network range before scanning.

### 3. Why is authorization important even when using publicly available security tools?

Authorization is important because publicly available tools can still be used to access or probe systems without permission. Having permission ensures that the assessment is lawful, controlled, and limited to approved targets.

---

# TASK 2: IDENTIFY THE ATTACKER'S STARTING POINT

### 1. What is the difference between an IP address and a subnet?

An IP address identifies a device on a network. A subnet is a logical division of a network that defines a range of IP addresses that belong together.

For example, 192.168.204.221 is an IP address. With a /24 prefix, its subnet is 192.168.204.0/24.

### 2. Why is network configuration important before host discovery?

Network configuration helps the analyst identify active interfaces, IP addresses, routes, and the correct subnet. This information ensures that discovery activities use the correct network interface and authorized network range.

### 3. Does a failed ping prove that a system is unavailable? Explain.

No. A failed ping does not necessarily mean that a system is unavailable. The target may block ICMP traffic, the network route may be incorrect, a firewall may block the packets, or the virtual machines may be connected to different virtual networks. The analyst should check the network configuration and use other authorized methods to verify connectivity.

---

# TASK 3: DISCOVER AUTHORIZED HOSTS

### 1. What does the -sn option do in Nmap?

The -sn option performs host discovery without carrying out a normal port scan. It helps identify which hosts appear to be online within the specified network range.

### 2. Why might a live host not appear in a host discovery scan?

A live host may not appear because its firewall blocks discovery probes, the host is temporarily unavailable, the analyst is using the wrong subnet or interface, or the virtual network is incorrectly configured. Different network conditions may also affect discovery results.

### 3. Why should analysts verify discovered assets with other evidence?

Analysts should verify discovered assets to reduce errors and avoid making unsupported assumptions. They can compare scan results with IP configuration, hostnames, network records, service information, and other authorized evidence. This helps confirm the identity and role of each asset.

---

# TASK 4: IDENTIFY EXPOSED PORTS AND SERVICES

### 1. What is the difference between an open port and a vulnerable service?

An open port indicates that a service is accepting connections on a particular network port. A vulnerable service contains a weakness that could potentially be exploited. An open port does not automatically mean that the service is vulnerable.

### 2. Why might SSH be an expected service on a Linux server?

SSH allows authorized administrators to connect to and manage a Linux server remotely through an encrypted connection. It may be necessary for system administration, but access should be restricted to authorized users and networks.

### 3. Why can an unnecessary service increase the attack surface?

An unnecessary service creates an additional point that an attacker could investigate or attempt to access. If the service is outdated, misconfigured, or poorly protected, it may introduce additional security risks. Disabling services that are not required can reduce exposure.

### 4. Why should analysts avoid assuming that a particular port always identifies a specific application?

Port numbers are commonly associated with particular services, but applications can use different ports, and different applications can use the same port. Analysts should verify the actual service using evidence such as Nmap service detection and the target's configuration.

---

# TASK 5: INSPECT LISTENING SERVICES LOCALLY

### 1. What does a listener bound to 127.0.0.1 imply?

A service bound to 127.0.0.1 listens on the local loopback interface. It is generally accessible only from the same machine rather than directly through its network interfaces.

### 2. Why might a service be listening locally but not reachable remotely?

A service may be listening only on the loopback interface. A firewall may also block incoming connections, routing may be incorrect, or network segmentation may prevent remote access. Therefore, a local listener does not automatically mean that a remote host can reach it.

### 3. Why should analysts compare local service information with network scan results?

Comparing the two sources helps analysts understand which services are running locally and which are reachable over the network. Differences may result from firewall rules, interface bindings, routing, or network restrictions. This comparison helps identify unexpected exposure and verify findings.

---

# TASK 10: FINAL QUESTIONS

### 1. Define attack surface and explain how it differs from an attack vector.

An attack surface is the collection of systems, services, applications, accounts, interfaces, and other points that could potentially be targeted. An attack vector is the method or path used to attempt an attack. For example, an exposed SSH service may be part of the attack surface, while attempting unauthorized access through SSH is an attack vector.

### 2. Why is asset inventory a prerequisite for effective vulnerability management?

An asset inventory helps an organization identify the systems it owns, their roles, and the software and services they use. Without an accurate inventory, important systems may be overlooked, vulnerabilities may remain undiscovered, and security updates may not be applied where needed.

### 3. Does every open port represent a security vulnerability? Explain.

No. An open port may support a legitimate business or administrative function. Its risk depends on the service, configuration, access controls, software version, network exposure, and purpose. Analysts must investigate the service and its context before deciding whether it represents a security weakness.

### 4. How can an exposed SSH service increase an organization's attack surface?

An exposed SSH service creates a remote entry point to a system. If it is poorly configured, uses weak authentication, runs vulnerable software, or permits excessive access, an attacker may attempt to compromise it. Restricting SSH access and using strong authentication can reduce the risk.

### 5. Why is a service's network binding important?

Network binding determines the network interface or address on which a service listens. A service bound to 127.0.0.1 is generally local-only, while a service bound to a wildcard address may listen on multiple interfaces. Correct binding helps prevent services from being accessible through interfaces where access is unnecessary.

### 6. What is the difference between a discovered exposure and a confirmed vulnerability?

A discovered exposure is an observed point of access, such as an open port or a remotely reachable service. A confirmed vulnerability is a verified security weakness that can affect the system under the relevant conditions. An exposure may create risk, but further investigation is needed before concluding that a vulnerability exists.

### 7. How can network segmentation reduce attack surface and limit the impact of a compromised machine?

Network segmentation separates systems into different network areas and restricts communication between them. If one machine is compromised, appropriate segmentation and firewall rules can prevent or limit access to other systems. Vulnerable training machines should be isolated from production systems and unrelated networks.

### 8. What evidence should an analyst collect before recommending that a service be disabled?

The analyst should collect the service name, listening port, software version where available, affected asset, network scan results, local listening-socket information, and evidence of whether the service is required. The analyst should also consider business needs, dependencies, access controls, and the possible operational impact of disabling it.

### 9. Why should an attack surface assessment be repeated after infrastructure changes?

Infrastructure changes can introduce new systems, ports, applications, routes, accounts, or configurations. Repeating the assessment helps identify newly exposed services, verify that previous weaknesses have been corrected, and ensure that the asset inventory remains accurate.

### 10. If you were the security analyst, which finding would you remediate first and why?

I would first remediate a verified, high-risk exposure that has a high likelihood of exploitation and could cause significant damage. For example, an unnecessarily exposed, outdated remote-access service on a critical system may require urgent attention. I would confirm the evidence, assess the business impact, and prioritize remediation based on risk rather than the port number alone.

---

# IMPORTANT NOTES ABOUT MY LAB FINDINGS

The answers above explain the concepts. The following technical findings must be completed from my actual lab evidence.

1. **Network configuration:** Record the output of `ip -br addr` and `ip route`.
2. **Connectivity:** Record whether the approved target responds to the connectivity test.
3. **Host discovery:** Record the actual Nmap results and verified hosts.
4. **Open ports and services:** Record the actual ports, service names, and versions reported by Nmap.
5. **Local listeners:** Record the output of `sudo ss -lntup` on the relevant target machine.
6. **Risk assessment:** Assign likelihood, impact, and risk scores based on evidence.
7. **Wireshark:** Record what the authorized lab capture shows.
8. **Remediation:** Recommend controls that match the actual findings.



---

# CONCLUSION

Attack surface mapping helps an organization understand which assets it operates, which services are reachable, and where security risks may exist. A reliable assessment requires an approved scope, accurate discovery, verification of services, evidence-based risk assessment, and practical remediation recommendations.

By maintaining an accurate asset inventory, restricting unnecessary network access, updating software, enforcing strong authentication, segmenting networks, and repeating assessments after changes, an organization can reduce its attack surface and improve its security posture.

