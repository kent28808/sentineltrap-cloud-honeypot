# 🛡️ SentinelTrap – Azure Honeypot Detection & Incident Response Lab

> **An end-to-end Microsoft Azure cyber range that simulates a real-world compromise of an internet-facing Windows server, leveraging Microsoft Defender for Endpoint, Microsoft Sentinel, Azure Monitor, and MySQL audit logging to detect, investigate, and respond to attacker activity.**

---

## 📖 Overview

This project demonstrates the complete lifecycle of detecting and responding to a real-world cyber attack in Microsoft Azure. A Windows 11 virtual machine hosting a MySQL database was intentionally hardened, instrumented for telemetry, and then deliberately weakened and exposed to the public internet to attract malicious activity.

Using Microsoft Defender for Endpoint, Microsoft Sentinel, Azure Monitor, and Azure Log Analytics, I collected telemetry, built custom detection rules, investigated attacker behavior, contained the compromise, and documented the incident.

Unlike a traditional honeypot demonstration, this project emphasizes the complete defensive workflow from secure deployment and detection engineering to threat hunting, incident response, and recovery.

---

## 🎯 Objectives

- Deploy an internet-facing Windows virtual machine
- Configure MySQL audit logging
- Centralize endpoint and database telemetry
- Build custom Microsoft Sentinel analytics rules
- Detect real-world attacker activity
- Perform threat hunting using Microsoft Defender XDR
- Investigate attacker behavior
- Contain and recover from the compromise
- Produce an end-to-end incident report

---

## 🛠️ Technologies Used

| Category | Technologies |
|----------|--------------|
| Cloud | Microsoft Azure |
| SIEM | Microsoft Sentinel |
| Endpoint Detection | Microsoft Defender for Endpoint |
| Monitoring | Azure Monitor |
| Log Management | Azure Log Analytics |
| Operating System | Windows 11 |
| Database | MySQL Community Server |
| Query Language | Kusto Query Language (KQL) |
| Database Language | SQL |

---

## 🏗️ Architecture

<img width="1536" height="1024" alt="Honeypotarch" src="https://github.com/user-attachments/assets/43c1ffca-7429-420a-9010-036792c235f9" />

*Figure 1. High-level architecture of the Azure SOC honeypot, showing the Windows virtual machine, centralized telemetry collection through Azure Monitor Agent and Microsoft Sentinel, GeoIP enrichment, attack visualization, and security monitoring and detection workflows.*

The environment consists of:

- Windows 11 Virtual Machine
- MySQL Database
- Azure Monitor Agent
- Microsoft Defender for Endpoint
- Azure Log Analytics Workspace
- Microsoft Sentinel

Telemetry from both Windows and MySQL is centralized into Azure Log Analytics, where Microsoft Sentinel uses custom analytics rules to detect suspicious authentication activity and generate security incidents.

---

# 🔄 Project Workflow

## Phase 1 — Build & Harden

A Windows virtual machine was deployed in Azure with Microsoft Defender for Endpoint onboarded while inbound internet access remained blocked.

### Tasks Completed

- Created Windows 11 VM
- Configured Public IP
- Enforced inbound deny rules
- Onboarded VM to Microsoft Defender for Endpoint
- Verified Defender telemetry

<img width="1670" height="1275" alt="azurevm" src="https://github.com/user-attachments/assets/c20d7703-7959-48f1-8d6d-7d1926739a2b" />

*Figure 2. Azure Virtual Machine overview of the Windows 11 honeypot, documenting the initial compute, networking, and public-facing configuration before telemetry collection, detection engineering, and controlled exposure.*

<br><br>
<img width="1668" height="1135" alt="Defender1" src="https://github.com/user-attachments/assets/245510c2-ee60-44ac-a9b3-ca9bab97864f" />

*Figure 3. Microsoft Defender for Endpoint Device Inventory showing the deployed Windows 11 honeypot and confirming successful endpoint onboarding and active telemetry reporting.*

<br><br>
<img width="1094" height="757" alt="Defender2" src="https://github.com/user-attachments/assets/266a7f37-c02a-467a-97f9-fb352d3d3ae4" />

*Figure 4. Microsoft Defender Advanced Hunting DeviceInfo results validating telemetry from the Windows 11 honeypot and confirming the endpoint's identity and operating system information.*

---

## Phase 2 — Configure MySQL

Installed MySQL Community Server and populated a sample corporate database. Enabled general query logging and authentication logging to capture all database activity.

### Tasks Completed

- Installed MySQL
- Imported sample database
- Enabled General Query Log
- Verified logging functionality

<img width="1197" height="972" alt="mysql1" src="https://github.com/user-attachments/assets/02e3452f-c5fc-4ca0-a519-8a27df539ccf" />

*Figure 5. MySQL Workbench connected to the honeypot database, showing the populated kt_corp schema and successful query execution against simulated corporate data.*

<img width="1182" height="954" alt="mysql2" src="https://github.com/user-attachments/assets/a64cb0e5-3fcc-4e51-9b2c-ebec3f326138" />

*Figure 6. mysql_general.log capturing authentication and query activity generated during database validation, confirming that database events were being recorded for subsequent security monitoring and centralized log collection.*

---

## Phase 3 — Centralize Logging

Configured Azure Monitor Agent and a Data Collection Rule (DCR) to ingest MySQL audit logs into Azure Log Analytics.

### Tasks Completed

- Installed Azure Monitor Agent
- Created Data Collection Rule
- Forwarded MySQL logs
- Verified telemetry ingestion

### 📷 Screenshots

- [ ] Data Collection Rule
- [ ] Azure Monitor Agent Extension
- [ ] MySQLAudit_CL Table
- [ ] Log Analytics Query Results

---

## Phase 4 — Detection Engineering

Developed custom Microsoft Sentinel Analytics Rules using Kusto Query Language (KQL) to detect suspicious authentication activity.

### Detection Rules

- Windows Administrator Logon
- Windows Guest Logon
- Successful MySQL Authentication
- Failed MySQL Authentication
- Database Query Monitoring

### 📷 Screenshots

- [ ] Sentinel Analytics Rules
- [ ] KQL Detection Queries
- [ ] Entity Mapping
- [ ] Rule Configuration

---

## Phase 5 — Controlled Exposure

After validating detections, the environment was intentionally weakened and exposed to the public internet to attract real attacker traffic.

### Security Controls Modified

- Enabled Administrator account
- Enabled Guest account
- Configured weak credentials
- Disabled Windows Firewall
- Allowed inbound NSG traffic
- Exposed MySQL externally

The exposure timestamp was documented to measure the time-to-compromise.

### 📷 Screenshots

- [ ] Network Security Group
- [ ] Windows Firewall Disabled
- [ ] MySQL Remote Authentication
- [ ] Exposure Timestamp

---

## Phase 6 — Threat Detection

Monitored attacker activity using Microsoft Defender and Microsoft Sentinel after exposing the environment.

### Data Sources

- DeviceLogonEvents
- DeviceProcessEvents
- DeviceNetworkEvents
- DeviceFileEvents
- DeviceRegistryEvents
- NTANetAnalytics
- MySQLAudit_CL

### 📷 Screenshots

- [ ] Sentinel Incident
- [ ] Device Logon Events
- [ ] MySQL Authentication Events
- [ ] Network Events
- [ ] MySQL Queries

---

## Phase 7 — Incident Investigation

Investigated attacker behavior by correlating endpoint telemetry with database activity.

### Investigation Focus

- Initial Access
- Authentication Attempts
- Process Execution
- Database Access
- Network Activity
- Discovery
- Collection

### 📷 Screenshots

- [ ] Defender Advanced Hunting
- [ ] Process Tree
- [ ] Device Timeline
- [ ] MySQL Query Activity

---

## 📊 Attack Timeline

| Time | Event |
|------|-------|
| **YYYY-MM-DD HH:MM UTC** | Environment exposed |
| **YYYY-MM-DD HH:MM UTC** | First inbound scan detected |
| **YYYY-MM-DD HH:MM UTC** | First failed RDP authentication |
| **YYYY-MM-DD HH:MM UTC** | First successful logon |
| **YYYY-MM-DD HH:MM UTC** | MySQL authentication |
| **YYYY-MM-DD HH:MM UTC** | Database queries observed |
| **YYYY-MM-DD HH:MM UTC** | Device isolated |

---

## Phase 8 — Containment

After sufficient evidence was collected, the compromised endpoint was isolated using Microsoft Defender for Endpoint.

### Actions Performed

- Isolated endpoint
- Captured Investigation Package
- Preserved forensic evidence

### 📷 Screenshots

- [ ] Device Isolation
- [ ] Defender Response Actions
- [ ] Investigation Package

---

## Phase 9 — Recovery

Following investigation, security controls were restored.

### Recovery Actions

- Enabled Windows Firewall
- Hardened Administrator account
- Disabled Guest account
- Restricted NSG rules
- Removed public MySQL access
- Restored database backup

---

# 📈 Skills Demonstrated

- Microsoft Sentinel
- Microsoft Defender for Endpoint
- Azure Monitor
- Azure Log Analytics
- Detection Engineering
- Threat Hunting
- Incident Response
- Security Monitoring
- Digital Forensics
- Kusto Query Language (KQL)
- SQL
- Windows Security
- MySQL Administration
- Cloud Security

---

# 💡 Key Takeaways

This project demonstrates an end-to-end Security Operations Center (SOC) workflow. Rather than simply deploying a vulnerable system, the project emphasizes designing detections before exposure, collecting telemetry from multiple sources, investigating real attacker behavior, and performing containment and recovery using Microsoft Defender and Microsoft Sentinel.

The lab provided practical experience with cloud security monitoring, detection engineering, incident response, and threat hunting in a Microsoft security environment.

---

# 📚 MITRE ATT&CK Mapping

| Tactic | Technique |
|---------|-----------|
| Initial Access | Valid Accounts (T1078) |
| Credential Access | Brute Force (T1110) |
| Discovery | System Information Discovery (T1082) |
| Discovery | Account Discovery (T1033) |
| Collection | Data from Local System (T1005) |
| Command & Control | Network Connections (Observed) |

---

# 📂 Repository Structure

```
SentinelTrap-Cloud-Honeypot-Lab/
│
├── images/
├── kql/
├── queries/
├── reports/
├── screenshots/
├── README.md
└── LICENSE
```

---

# 🙏 Acknowledgements

This project was completed as part of a cloud cybersecurity capstone focused on Microsoft Sentinel, Microsoft Defender for Endpoint, threat hunting, and incident response.
