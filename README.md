# Enterprise Cybersecurity Homelab

> A hands-on red-vs-blue simulation lab built from scratch using VirtualBox, designed to model how real-world enterprises defend against modern cyber threats.

<p align="center">
  <img src="https://img.shields.io/badge/VirtualBox-Homelab-blue" alt="VirtualBox Homelab">
  <img src="https://img.shields.io/badge/Active%20Directory-Domain%20Lab-blue" alt="Active Directory">
  <img src="https://img.shields.io/badge/Wazuh-XDR%20%2B%20SIEM-green" alt="Wazuh">
  <img src="https://img.shields.io/badge/Security%20Onion-IDS%20%2B%20NSM-orange" alt="Security Onion">
  <img src="https://img.shields.io/badge/Kali%20Linux-Red%20Team-red" alt="Kali Linux">
  <img src="https://img.shields.io/badge/Status-Active%20Lab-lightgrey" alt="Active Lab">
</p>

---

## Overview

This project recreates a full enterprise environment from the ground up, complete with Windows and Linux endpoints, centralised identity and access control, security monitoring infrastructure, and a red-team attack simulation.

The environment is intentionally vulnerable and instrumented with open-source tools, such as Wazuh and Security Onion, to detect, log, and respond to each stage of an attack. This project has been set up to test these open-source tools and learn practically in a virtual enterprise network, gaining hands-on exposure to both offensive and defensive security workflows.

Stay tuned as there is more to come in the future.

---

## Contents

<p align="center">
  <a href="#lab-setup-network-topology">Topology</a> •
  <a href="#technologies-used">Technologies</a> •
  <a href="#key-concepts-covered">Concepts</a> •
  <a href="#build-process">Build Process</a> •
  <a href="#attack-simulation-flow">Attack Simulation</a> •
  <a href="#detection-engineering">Detection Engineering</a> •
  <a href="#outcome">Outcome</a>
</p>

---

## Lab Setup: Network Topology

<p align="center">
  <img src="https://i.imgur.com/tjq6LVn.png" alt="Network Topology Diagram" width="850">
</p>

<p align="center"><em>Figure 1. Full lab network topology</em></p>

---

## Technologies Used

<div align="center">

| Category | Tools / OS |
|:---:|:---:|
| Hypervisor | VirtualBox — NAT Network: `project-network` |
| OS & Domain | Windows Server 2025, Windows 11 Enterprise |
| Linux Systems | Ubuntu Desktop 22.04, Ubuntu Server 22.04 |
| Attacker | Kali Linux |
| Email Service | Mailhog — SMTP |
| Security Stack | Wazuh — XDR, SIEM, Vulnerability Scanner |
| Network Security Monitoring | Security Onion — IDS, NSM, Zeek, Suricata |

</div>

---

## Key Concepts Covered

- Domain Controller and Active Directory using LDAP/Kerberos
- DNS and DHCP configuration
- Linux and Windows domain integration
- Network intrusion detection using Zeek and Suricata
- Centralised log analysis using Wazuh and Elastic Stack
- File Integrity Monitoring
- SMTP and internal phishing attack delivery
- Red team simulation using Hydra, Evil-WinRM, and phishing
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
- Built Security Server `sec-box` with Ubuntu clone

### 3. Security Stack Deployment

- Installed and configured Wazuh Indexer, Server, and Dashboard
- Deployed agents to Windows and Linux endpoints
- Deployed Security Onion with Zeek, Suricata, and Kibana
- Connected alert sources to the SIEM dashboard

<p align="center">
  <img src="https://i.imgur.com/BahvCou.jpeg" alt="All machines set up and running" width="850">
</p>

<p align="center"><em>Figure 2. All lab machines running and connected</em></p>

---

## Attack Simulation Flow

### Phase 1: Reconnaissance & Initial Access

- **Nmap Scan** → Identified open services on corporate server
- **Hydra Brute Force** → Cracked `root:november` via SSH
- **Phishing Email** → Sent HTML lure to `janed@linux-client`
- **Credential Capture** → Hosted fake login site on Apache2

### Phase 2: Lateral Movement & Privilege Escalation

- SSH access as `janed` on Linux client
- Wazuh detected multiple failed logins using Rule 5760
- Credential reuse via **Evil-WinRM** on Windows client
- Privilege escalation to domain-level Administrator

### Phase 3: Data Exfiltration

- RDP into DC using `xfreerdp` with stolen credentials
- Accessed `secrets.txt` in `C:\ProductionFiles`
- Exfiltrated data to Kali using `scp`

### Phase 4: Persistence & Defense Evasion

- Created new Domain Admin account `project-user`
- Installed Scheduled Task with PowerShell reverse shell
- Alerts triggered in Wazuh for new user activity and FIM detection

<p align="center">
  <img src="https://i.imgur.com/t0YTmiL.png" alt="Attack Flow Diagram" width="850">
</p>

<p align="center"><em>Figure 3. Attack flow diagram from recon to persistence</em></p>

<p align="center">
  <img src="https://i.imgur.com/UTLOtgh.png" alt="Phishing Site" width="550">
</p>

<p align="center"><em>Figure 4. Phishing site hosted on attacker’s machine</em></p>

---

## Detection Engineering

<p align="center">
  <img src="https://i.imgur.com/kCthW1V.png" alt="Wazuh Dashboard" width="850">
</p>

<p align="center"><em>Figure 5. Wazuh dashboard overview showing active alerts</em></p>

<div align="center">

| Event | Detection Rule / Method |
|:---:|:---:|
| Failed SSH login attempts | Wazuh Rule ID 5760 |
| WinRM Authentication | Wazuh Rule ID 60106 |
| RDP Login | Wazuh Rule ID 92653 |
| Sensitive File Modification | Custom Rule ID 100001 via FIM |

</div>

All logs were correlated in both Wazuh and Security Onion. Alerts were confirmed in real-time via dashboards.

<p align="center">
  <img src="https://i.imgur.com/H28pDzy.png" alt="Wazuh Alerts" width="850">
</p>

<p align="center"><em>Figure 6. Sample Wazuh alerts triggered during attack simulation</em></p>

---

## Outcome

This lab models the full kill chain of a targeted intrusion, from initial access to privilege escalation, data theft, and persistence, while tracking the attack through detection tools.

The project demonstrates how layered defence, log analysis, and SIEM tuning work in practice.

**My key takeaways are:**

- Gained hands-on experience with real-time detection using Wazuh and Security Onion
- Understood how misconfigurations and weak credentials can lead to full domain compromise
- Learned to build and monitor alerts using both behavioural FIM and signature-based rules
- Developed a stronger grasp of how Windows and Linux systems integrate in enterprise networks
- Practiced simulating and tracing attacker techniques aligned with the MITRE ATT&CK framework

---

## Credits

This project was inspired by Grant Collins: [YouTube](https://www.youtube.com/@collinsinfosec).

---

## Related Project

**View my home network architecture from where these projects are run:**

[Home Network Setup](https://github.com/nathansamuel92/Home-Network-Setup/blob/main/README.md)
