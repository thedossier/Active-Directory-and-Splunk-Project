# Active Directory & Splunk Project

This project demonstrates the setup and integration of **Active Directory**, **Splunk Enterprise**, and **Sysmon** within a virtualized lab environment.  
The objective was to simulate an enterprise-style SOC (Security Operations Center) environment for hands-on experience in **log collection, analysis, and security event monitoring**.

---

## 🧠 Project Overview

I created a virtual environment using **VirtualBox** to deploy:
- **Windows Server 2022** (Domain Controller)
- **Windows 10 Pro** (Endpoint / Target Machine)
- **Kali Linux** (Attacker / Red Team Machine)
- **Ubuntu Server** (Splunk Enterprise Server)

This environment was designed to:
- Establish an **Active Directory domain** for centralized authentication via **Kerberos**
- Deploy **Sysmon** and the **Splunk Universal Forwarder** on Windows endpoints
- Collect, forward, and visualize event logs and Sysmon telemetry in **Splunk**
- Practice fundamental SOC and Blue Team analysis workflows

---

## 🏗️ Environment Setup

### Virtualization
- Platform: **VirtualBox 7.0.10**
- Verification: Confirmed file integrity using PowerShell  
  ```powershell
  Get-FileHash .\VirtualBox-7.0.10-158379-Win.exe
  ```
- Created a NAT network (`ad-project`) to allow VM communication:
  - Windows Server 2022 — Domain Controller
  - Windows 10 Pro — Workstation (Target)
  - Kali Linux — Attacker
  - Ubuntu Server — Splunk Server

---

## ⚙️ Active Directory Setup

1. Installed **Active Directory Domain Services (AD DS)** on Windows Server 2022.  
2. Promoted the server to a **Domain Controller** for Kerberos-based authentication.  
3. Configured and verified user, group, and computer objects within AD DS.

---

## 💾 Splunk Server (Ubuntu)

1. Updated and upgraded packages:
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```
2. Installed VirtualBox guest utilities and added shared folders.
3. Set a static IP using Netplan:
   ```bash
   sudo nano /etc/netplan/50-cloud-init.yaml
   ```
   *(Ensured proper YAML indentation using spaces, not tabs.)*
4. Installed Splunk Enterprise via `.deb` package:
   ```bash
   sudo dpkg -i splunk*.deb
   ```
5. Started Splunk and enabled boot-start:
   ```bash
   cd /opt/splunk/bin
   sudo ./splunk start
   sudo ./splunk enable boot-start -user splunk
   ```

---

## 🧩 Endpoint Configuration (Windows 10 & Server)

1. Configured static IPs according to the network diagram.  
2. Installed **Splunk Universal Forwarder**:
   - Configured the receiving indexer as `192.168.10.10:9997`
3. Installed **Sysmon** with **Olaf’s Sysmon Config**:
   ```powershell
   .\Sysmon64.exe -i ..\sysmonconfig.xml
   ```
4. Updated `inputs.conf`:
   ```ini
   [WinEventLog://Application]
   index = endpoint
   disabled = false

   [WinEventLog://Security]
   index = endpoint
   disabled = false

   [WinEventLog://System]
   index = endpoint
   disabled = false

   [WinEventLog://Microsoft-Windows-Sysmon/Operational]
   index = endpoint
   disabled = false
   renderXml = true
   source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
   ```
5. Restarted SplunkForwarder service to apply changes.

---

## 📊 Splunk Configuration

1. Created a new index named `endpoint`.
2. Configured Splunk to receive data on port `9997`:
   ```
   Settings > Forwarding and Receiving > Configure Receiving > New Receiving Port
   ```
3. Verified incoming logs via:
   ```
   Search: index=endpoint
   ```

---

## 🧠 Skills Demonstrated

- Active Directory setup and domain management  
- Network configuration (NAT, static IPs)  
- Log forwarding and collection architecture  
- Sysmon event logging and Olaf configuration tuning  
- Splunk index management and search queries  
- Troubleshooting Linux and YAML indentation errors  
- PowerShell and Bash proficiency  

---

## 🧰 Tools Used
| Category | Tools |
|-----------|--------|
| Virtualization | VirtualBox |
| OS | Windows Server 2022, Windows 10 Pro, Ubuntu Server, Kali Linux |
| SIEM | Splunk Enterprise, Splunk Universal Forwarder |
| Telemetry | Sysmon (with Olaf config) |
| Languages | PowerShell, Bash |
| Protocols | Kerberos, TCP/IP, DNS |

---

## 🚧 Next Steps

- Configure **Group Policy Objects (GPOs)** for centralized security settings  
- Integrate **attack simulation** using Kali (brute force, Mimikatz, etc.)  
- Set up **Splunk dashboards and correlation searches** for threat detection  
- Export notable events and create mock SOC reports

---

## 🏁 Summary

This project replicates a **realistic enterprise monitoring environment** using free tools and virtualization. It demonstrates the ability to:
- Deploy, configure, and secure multi-OS systems
- Implement end-to-end telemetry and log ingestion
- Build a foundation for SOC analysis and threat hunting practice

---

### 📸 Screenshots
*(To be added as project progresses)*

---

### 👤 Author
**The Dossier**  
Aspiring SOC Analyst | Home Lab Enthusiast | Cybersecurity Learner  
