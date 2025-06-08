# Enterprise Cybersecurity Homelab (Project X)

> A hands-on red-vs-blue simulation lab built from scratch using VirtualBox, designed to model how real-world enterprises defend against modern cyber threats.

## Overview

This project recreates a full enterprise environment from the ground up — complete with Windows and Linux endpoints, centralized identity and access control, security monitoring infrastructure, and a red-team attack simulation. The environment is intentionally vulnerable and instrumented with open-source tools like Wazuh and Security Onion to detect, log, and respond to each stage of an attack.

It’s designed for students, professionals, and enthusiasts looking to get practical exposure to both offensive and defensive security workflows.

---

## 🖥️ Lab Setup (Network Topology)

> ![Network Topology Diagram](https://i.imgur.com/iBxCrUd.png)

---

## 🔧 Technologies Used

| Category        | Tools / OS                                     |
|----------------|-------------------------------------------------|
| Hypervisor      | VirtualBox (NAT Network: `project-x-network`)  |
| OS & Domain     | Windows Server 2025, Windows 11 Enterprise     |
| Linux Systems   | Ubuntu Desktop 22.04, Ubuntu Server 22.04      |
| Attacker        | Kali Linux                                     |
| Email Service   | Postfix (SMTP)                                 |
| Security Stack  | Wazuh (XDR + SIEM), Security Onion (IDS/NSM)  |

---

## 💡 Key Concepts Covered

- Domain Controller + Active Directory (LDAP/Kerberos)
- DNS + DHCP configuration
- Linux ↔ Windows domain integration
- Network intrusion detection (Suricata, Zeek)
- Centralized log analysis (Wazuh + Elastic Stack)
- File Integrity Monitoring (FIM)
- SMTP and internal phishing attack delivery
- Real-world red team simulation (Hydra, Evil-WinRM, phishing)
- SOC-style alert correlation and response

---

## 🛠️ Build Process

### 1. Infrastructure Provisioning
- Set up all VMs in VirtualBox using NAT networking
- Assigned static IPs and DNS to each machine
- Took snapshots for rollback safety

### 2. Core Services Setup
- Promoted Windows Server to Domain Controller
- Joined Windows 11 and Ubuntu clients to domain
- Installed and configured Postfix for internal SMTP
- Built Security Server (sec-box) with Ubuntu clone

### 3. Security Stack Deployment
- Installed and configured Wazuh (Indexer, Server, Dashboard)
- Deployed agents to Windows and Linux endpoints
- Deployed Security Onion (Zeek + Suricata + Kibana)
- Connected alert sources to SIEM dashboard

---

## 🚨 Attack Simulation Flow

### Phase 1: Recon & Initial Access
- 🔍 **Nmap Scan** → Identify open services on email server  
- 🔓 **Hydra Brute Force** → Cracked `root:november` via SSH  
- ✉️ **Phishing Email** → Sent HTML lure to `janed@linux-client`  
- 🪝 **Credential Capture** → Hosted fake login site on Apache2  

### Phase 2: Lateral Movement
- 🐚 SSH access as `janed` on Linux client  
- 🧪 Wazuh detected multiple failed logins (Rule 5760)  
- 💳 Credential reuse via **Evil-WinRM** on Windows client  
- 🔁 Privilege escalation to domain-level Administrator

### Phase 3: RDP + Domain Controller Takeover
- 🖥️ RDP into DC using `xfreerdp` with stolen creds  
- 📁 Accessed `secrets.txt` in C:\ProductionFiles  
- 📤 **Exfiltrated** to Kali using `scp`

### Phase 4: Persistence
- 👤 Created new Domain Admin account `project-x-user`  
- 🕒 Installed Scheduled Task with PowerShell reverse shell  
- 🔔 Alerts triggered in Wazuh for new user + FIM detection

---

## 🔍 Detection Engineering

| Event                          | Detection Rule / Method             |
|-------------------------------|--------------------------------------|
| Failed SSH login attempts     | Wazuh Rule ID 5760                  |
| WinRM Authentication          | Wazuh Rule ID 60106                 |
| RDP Login                     | Wazuh Rule ID 92653                 |
| Sensitive File Modification   | Custom Rule ID 100002 via FIM       |

All logs were correlated in both Wazuh and Security Onion. Alerts were confirmed in real-time via dashboards.

> 📸 *Insert screenshots of alerts, dashboards, attack terminal logs here.*

---

## 🎯 Outcome

This lab models the full kill chain of a targeted intrusion — from initial access to privilege escalation, data theft, and persistence — all while tracking the attack through detection tools. The project demonstrates how layered defense, log analysis, and SIEM tuning work in practice.

Whether you're a student prepping for blue team roles or a red teamer brushing up on detection evasion, this homelab provides a high-fidelity enterprise simulation.

---

## 📷 Figures & Visuals

> Insert:
> - Network diagram
> - Attack chain timeline
> - Wazuh alert samples
> - Security Onion dashboard panels
> - s-nail/phishing terminal snapshots

---

## 📬 Contact

Built as part of a structured learning project on modern cyber offense and defense.  
Feel free to connect for collaboration or questions!


View my home network architecture from where these projects are run --> [Here](https://github.com/nathansamuel92/Home-Network-Setup/blob/main/README.md)

