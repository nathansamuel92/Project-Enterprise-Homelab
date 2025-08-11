# Enterprise Cybersecurity Homelab (Project X)

> A hands-on red-vs-blue simulation lab built from scratch using VirtualBox, designed to model how real-world enterprises defend against modern cyber threats. NOTE: This project closely follows the instructions presented in Grant Collin's [Project X](https://www.youtube.com/watch?v=f8SCF2UoT4c&t=5s) labs.

## Overview

This project recreates a full enterprise environment from the ground up, complete with Windows and Linux endpoints, centralised identity and access control, security monitoring infrastructure, and a red-team attack simulation. The environment is intentionally vulnerable and instrumented with open-source tools, such as Wazuh and Security Onion, to detect, log, and respond to each stage of an attack. This project has been set up to test these open-source tools and learn practically in a virtual enterprise network, gaining hands-on exposure to both offensive and defensive security workflows. Stay tuned as there is more to come in the future!

---

## Lab Setup (Network Topology)

![Network Topology Diagram](https://i.imgur.com/iBxCrUd.png)

---

## Technologies Used

| Category        | Tools / OS                                     |
|----------------|-------------------------------------------------|
| Hypervisor      | VirtualBox (NAT Network: `project-network`)  |
| OS & Domain     | Windows Server 2025, Windows 11 Enterprise     |
| Linux Systems   | Ubuntu Desktop 22.04, Ubuntu Server 22.04      |
| Attacker        | Kali Linux                                     |
| Email Service   | Mailhog (SMTP)                                 |
| Security Stack  | Wazuh (XDR + SIEM + Vuln-Scanner), Security Onion (IDS/NSM)  |

---

## Key Concepts Covered

- Domain Controller + Active Directory (LDAP/Kerberos)
- DNS + DHCP configuration
- Linux/Windows domain integration
- Network intrusion detection (Zeek, Suricata)
- Centralized log analysis (Wazuh + Elastic Stack)
- File Integrity Monitoring (FIM)
- SMTP and internal phishing attack delivery
- Real-world red team simulation (Hydra, Evil-WinRM, phishing)
- SOC-style alert correlation and response

---

## Build Process

### 1. Infrastructure Provisioning
- Set up VMs in VirtualBox through NAT networking
- Assigned static IPs and DNS to each machine
- Took snapshots for rollback safety

### 2. Core Services Setup
- Promoted Windows Server to Domain Controller
- Joined Windows 11 and Ubuntu clients to the domain
- Installed and configured Mailhog for virtual SMTP
- Built Security Server (sec-box) with Ubuntu clone

### 3. Security Stack Deployment
- Installed and configured Wazuh (Indexer, Server, Dashboard)
- Deployed agents to Windows and Linux endpoints
- Deployed Security Onion (Zeek + Suricata + Kibana)
- Connected alert sources to the SIEM dashboard

![All machines set up and running](https://i.imgur.com/BahvCou.jpeg)

---

## Attack Simulation Flow

### Phase 1: Reconnaissance & Initial Access
- **Nmap Scan** → Identify open services on corporate server  
- **Hydra Brute Force** → Cracked `root:november` via SSH  
- **Phishing Email** → Sent HTML lure to `janed@linux-client`  
- **Credential Capture** → Hosted fake login site on Apache2  

### Phase 2: Lateral Movement & Privilege Escalation
- SSH access as `janed` on Linux client  
- Wazuh detected multiple failed logins (Rule 5760)  
- Credential reuse via **Evil-WinRM** on Windows client  
- Privilege escalation to domain-level Administrator

### Phase 3: Data Exfiltration
- RDP into DC using `xfreerdp` with stolen creds  
- Accessed `secrets.txt` in C:\ProductionFiles  
- **Exfiltrated** to Kali using `scp`

### Phase 4: Persistence & Defense Evasion
- Created new Domain Admin account `project-user`  
- Installed Scheduled Task with PowerShell reverse shell  
- Alerts triggered in Wazuh for new user + FIM detection

---

![Attack Flow Diagram](https://i.imgur.com/8JPWNoF.png)

---

![Phishing Site](https://i.imgur.com/UTLOtgh.png)

---

## 🔍 Detection Engineering

![Wazuh Dashboard](https://i.imgur.com/kCthW1V.png)

| Event                          | Detection Rule / Method             |
|-------------------------------|--------------------------------------|
| Failed SSH login attempts     | Wazuh Rule ID 5760                  |
| WinRM Authentication          | Wazuh Rule ID 60106                 |
| RDP Login                     | Wazuh Rule ID 92653                 |
| Sensitive File Modification   | Custom Rule ID 100001 via FIM       |

All logs were correlated in both Wazuh and Security Onion. Alerts were confirmed in real-time via dashboards.

![Wazuh Alerts](https://i.imgur.com/H28pDzy.png)

---

## Outcome

This lab models the full kill chain of a targeted intrusion, from initial access to privilege escalation, data theft, and persistence. And all while tracking the attack through detection tools. The project demonstrates how layered defense, log analysis, and SIEM tuning work in practice.

**My key takeaways are:**

- Gained hands-on experience with real-time detection using Wazuh and Security Onion.
- Understood how misconfigurations and weak credentials can lead to full domain compromise.
- Learned to build and monitor alerts using both behavioral (FIM) and signature-based rules.
- Developed a stronger grasp of how Windows and Linux systems integrate in enterprise networks.
- Practiced simulating and tracing attacker techniques aligned with the MITRE ATT&CK framework.

Please show some love to Grant Collins, the reason this project was possible! Show him some love on YouTube, and stay tuned for his upcoming sequel project, which is a direct continuation of this project, however, focusing on networking and attacks.

---

View my home network architecture from where these projects are run --> [Here](https://github.com/nathansamuel92/Home-Network-Setup/blob/main/README.md)

