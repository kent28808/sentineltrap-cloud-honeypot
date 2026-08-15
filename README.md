<a name="readme-top"></a>

# 🛡️ SentinelTrap – Azure Honeypot Detection & Incident Response Lab

> **An end-to-end Microsoft Azure cyber range that simulates a real-world compromise of an internet-facing Windows server, leveraging Microsoft Defender for Endpoint, Microsoft Sentinel, Azure Monitor, and MySQL audit logging to detect, investigate, and respond to attacker activity.**

---

<a name="table-of-contents"></a>

## 📑 Table of Contents

- [Overview](#overview)
- [Incident at a Glance](#incident-at-a-glance)
- [Objectives](#objectives)
- [Technologies Used](#technologies-used)
- [Architecture](#architecture)
- [Project Workflow](#project-workflow)
  - [Phase 1: Build & Harden](#phase-1)
  - [Phase 2: Configure MySQL](#phase-2)
  - [Phase 3: Centralize Logging](#phase-3)
  - [Phase 4: Detection Engineering](#phase-4)
  - [Phase 5: Controlled Exposure](#phase-5)
  - [Phase 6: Threat Detection](#phase-6)
  - [Phase 7: Incident Investigation](#phase-7)
  - [Phase 8: Containment](#phase-8)
  - [Phase 9: Eradication and Recovery](#phase-9)
  - [Phase 10: Incident Reporting and Forensic Analysis](#phase-10)
    - [Final Reports](#final-reports)
    - [Geographic Attack Analysis](#geographic-attack-analysis)
- [Skills Demonstrated](#skills-demonstrated)
- [Key Takeaways](#key-takeaways)
- [MITRE ATT&CK Mapping](#mitre-attack-mapping)

---
<a name="overview"></a>

## 📖 Overview

This project demonstrates the complete lifecycle of detecting and responding to a real-world cyber attack in Microsoft Azure. A Windows 11 virtual machine hosting a MySQL database was intentionally hardened, instrumented for telemetry, and then deliberately weakened and exposed to the public internet to attract malicious activity.

Using Microsoft Defender for Endpoint, Microsoft Sentinel, Azure Monitor, and Azure Log Analytics, I collected telemetry, built custom detection rules, investigated attacker behavior, contained the compromise, and documented the incident.

Unlike a traditional honeypot demonstration, this project emphasizes the complete defensive workflow from secure deployment and detection engineering to threat hunting, incident response, and recovery.

---
<a name="incident-at-a-glance"></a>

## 🚨 Incident at a Glance

| Category | Finding |
|---|---|
| Exposure window | `2026-07-30T18:44:02Z` through `2026-08-11T16:37:48Z` |
| Affected asset | `corp-dc01` — Windows 11 Azure VM hosting MySQL |
| First confirmed database access | MySQL `root` authentication from `64.89.163.79` at `2026-07-31T01:36:02Z` |
| Database impact | Enumeration and reading of corporate tables, destructive `DROP TABLE` and `DROP DATABASE` operations, and creation of a Bitcoin ransom note |
| Endpoint activity | Successful external Administrator logons, Octo Browser installation, PowerShell-based system discovery, a Microsoft Defender exclusion, and outbound Octo-related traffic |
| Containment | Endpoint isolation through Microsoft Defender for Endpoint, removal of permissive NSG access, and post-isolation forensic collection |
| Recovery | Windows Firewall restored, Guest account disabled, and the `kt_corp` database restored and validated |
| Final reports | [Incident Response Report](./assets/SecurityIncidentReport.pdf) · [Host DFIR Report](./assets/HostDFIRReport.pdf) |

---
<a name="objectives"></a>

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
<a name="technologies-used"></a>

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
<a name="architecture"></a>

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

[Back to top](#readme-top)

---

<a name="project-workflow"></a>

## 🔄 Project Workflow

<a name="phase-1"></a>

### Phase 1: Build & Harden
A Windows virtual machine was deployed in Azure with Microsoft Defender for Endpoint onboarded while inbound internet access remained blocked.

#### Tasks Completed

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
<a name="phase-2"></a>

### Phase 2: Configure MySQL

Installed MySQL Community Server and populated a sample corporate database. Enabled general query logging and authentication logging to capture all database activity.

#### Tasks Completed

- Installed MySQL
- Imported sample database
- Enabled General Query Log
- Verified logging functionality

<img width="1197" height="972" alt="mysql1" src="https://github.com/user-attachments/assets/02e3452f-c5fc-4ca0-a519-8a27df539ccf" />

*Figure 5. MySQL Workbench connected to the honeypot database, showing the populated kt_corp schema and successful query execution against simulated corporate data.*

<br><br>

<img width="1182" height="954" alt="mysql2" src="https://github.com/user-attachments/assets/a64cb0e5-3fcc-4e51-9b2c-ebec3f326138" />

*Figure 6. mysql_general.log capturing authentication and query activity generated during database validation, confirming that database events were being recorded for subsequent security monitoring and centralized log collection.*

---
<a name="phase-3"></a>

### Phase 3: Centralize Logging

Configured Azure Monitor Agent and a Data Collection Rule (DCR) to ingest MySQL audit logs into Azure Log Analytics.

#### Tasks Completed

- Installed Azure Monitor Agent
- Created Data Collection Rule
- Forwarded MySQL logs
- Verified telemetry ingestion

<img width="1446" height="457" alt="dcr" src="https://github.com/user-attachments/assets/48452cd4-06d1-44ab-81c4-004d2b344c8f" />

*Figure 7. Azure Monitor Data Collection Rule configured to collect the MySQL general log and forward the telemetry to the LAW-Cyber-Range Log Analytics workspace for centralized security monitoring.*

<br><br>

<img width="1675" height="941" alt="Azureagent" src="https://github.com/user-attachments/assets/d3f9c324-5152-48eb-847e-a3d5178aa370" />

*Figure 8. Azure Monitor Agent successfully installed on the Windows honeypot VM, providing the telemetry collection mechanism used to forward the MySQL general query log to Azure Log Analytics.*
<br><br>

<img width="1188" height="981" alt="mysqlaudi" src="https://github.com/user-attachments/assets/1ca56ebe-862a-4351-a61b-c3c6ac691e18" />

*Figure 9. MySQLAudit_CL telemetry in Azure Log Analytics confirming successful ingestion of MySQL authentication and query events from the honeypot VM through Azure Monitor Agent and the configured Data Collection Rule.*

---
<a name="phase-4"></a>

### Phase 4: Detection Engineering

Developed custom Microsoft Sentinel Analytics Rules using Kusto Query Language (KQL) to detect suspicious authentication activity.

#### Detection Rules

- Windows Administrator Logon
- Windows Guest Logon
- Successful MySQL Authentication
- Failed MySQL Authentication
- Database Query Monitoring

<img width="1639" height="1208" alt="10r1" src="https://github.com/user-attachments/assets/81a46e6d-50bd-4516-893e-fe647080b62b" />
*Figure 10. Microsoft Sentinel analytics rule designed to detect successful Windows logons to the honeypot, using Defender endpoint telemetry and KQL to identify Administrator and Guest account activity.*

<br><br>
<img width="1423" height="665" alt="11r1" src="https://github.com/user-attachments/assets/ec9b70e2-17c5-4521-9e36-c22e1e9a8825" />   
*Figure 11. KQL validation of the Microsoft Sentinel MySQL authentication rule, classifying successful and failed connection attempts in `MySQLAudit_CL` and extracting the associated username and source host.*

---
<a name="phase-5"></a>

### Phase 5: Controlled Exposure

After validating detections, the environment was intentionally weakened and exposed to the public internet to attract real attacker traffic.

#### Security Controls Modified

- Enabled Administrator account
- Enabled Guest account
- Configured weak credentials
- Disabled Windows Firewall
- Allowed inbound NSG traffic
- Exposed MySQL externally

The exposure timestamp was documented to measure the time-to-compromise: 2026-07-30T18:44:02.1690712Z

<img width="1046" height="415" alt="users" src="https://github.com/user-attachments/assets/52879347-d1fa-4262-8af6-3173175d45f2" />

*Figure 12. Windows local account configuration showing the Administrator and Guest accounts enabled as part of the controlled honeypot exposure, creating intentionally weakened authentication controls for subsequent attack detection.*

<br><br>
<img width="1335" height="222" alt="inbound" src="https://github.com/user-attachments/assets/5e019db5-6f27-4482-a883-90ec5f06bb6e" />
*Figure 13. Azure Network Security Group configuration showing the honeypot intentionally exposed to inbound Internet traffic, increasing its discoverability and allowing external attack activity to reach the environment.*

<br><br>
<img width="883" height="398" alt="firewall" src="https://github.com/user-attachments/assets/9e291d6c-b0a7-4f59-97a8-1b5ead63d032" />     
*Figure 14. Windows Defender Firewall intentionally disabled as part of the controlled exposure phase, removing a host-level network control to increase the honeypot's visibility and attack surface.*

---
<a name="phase-6"></a>

### Phase 6: Threat Detection

Monitored attacker activity using Microsoft Defender and Microsoft Sentinel after exposing the environment.

#### Data Sources

- DeviceLogonEvents
- DeviceProcessEvents
- DeviceNetworkEvents
- DeviceFileEvents
- DeviceRegistryEvents
- NTANetAnalytics
- MySQLAudit_CL

<img width="1462" height="1221" alt="incident" src="https://github.com/user-attachments/assets/6eb4de7d-36c3-46ea-ad37-ca8ad70f01d2" />
*Figure 15. Microsoft Sentinel incident generated from honeypot telemetry, demonstrating detection of suspicious authentication activity following controlled exposure of the Windows environment.*

<br><br>
<img width="1517" height="1193" alt="windows" src="https://github.com/user-attachments/assets/dca8729e-4cab-4bc0-8309-1b8816f6a793" />
*Figure 16. Microsoft Defender DeviceLogonEvents telemetry showing remote authentication activity against the exposed Windows honeypot, including targeted accounts, source IP addresses, authentication results, and event timestamps.*

<br><br>
<img width="1495" height="1185" alt="mysql1" src="https://github.com/user-attachments/assets/d02ed718-a899-4825-aeac-6464132c2e4d" />
*Figure 17. MySQLAudit_CL telemetry showing external authentication activity against the exposed MySQL service, including connection timestamps, usernames, and source IP addresses.*

<br><br>
<img width="1505" height="1196" alt="mysql2" src="https://github.com/user-attachments/assets/bf0615f5-467f-4ffa-95b7-1075ad88ebc7" />
*Figure 18. MySQL query telemetry collected in MySQLAudit_CL following authentication, providing visibility into database activity performed against the honeypot and supporting post-compromise investigation.*

---
<a name="phase-7"></a>

### Phase 7: Incident Investigation

Investigated attacker behavior by correlating endpoint telemetry with database activity.

#### Investigation Focus

- Initial Access
- Authentication Attempts
- Process Execution
- Database Access
- Network Activity
- Discovery
- Collection

<img width="1501" height="1168" alt="19" src="https://github.com/user-attachments/assets/167e9661-ad39-40cc-8032-5ae1f886ce51" />
*Figure 19. Defender Advanced Hunting authentication activity. Authentication telemetry was queried following the documented exposure timestamp to identify successful and failed logon activity, associated accounts, remote source IP addresses, and authentication protocols.*

<br><br>
<img width="1289" height="1220" alt="20" src="https://github.com/user-attachments/assets/c9aa42de-d404-44ef-89cb-88cd2c5844e4" />
*Figure 20. Microsoft Defender process-tree investigation showing Octo Browser spawning PowerShell. The PowerShell command queried `Win32_ComputerSystemProduct` to retrieve the system UUID, behavior consistent with MITRE ATT&CK System Information Discovery (T1082). Portal timestamps are displayed in local time.*

<br><br>
<a name="attack-timeline"></a>

#### 📊 Attack Timeline

| Timestamp (UTC) | Phase | Observed Activity |
|---|---|---|
| `2026-07-30 18:44:02` | Controlled exposure | The documented exposure and incident-analysis window began. |
| `2026-07-31 01:36:02` | Initial database access | External source `64.89.163.79` successfully authenticated to MySQL as `root`. |
| `2026-07-31 01:36:12–01:36:28` | Discovery and collection | The session enumerated databases and accessed the `credentials`, `customers`, `orders`, and `payments` tables. |
| `2026-07-31 01:36:27–01:38:00` | Destructive impact | Tables and databases were deleted, and a `RECOVER_YOUR_DATA` ransom artifact was created. |
| `2026-07-31 18:13:35` | Endpoint access | The first confirmed external `RemoteInteractive` Administrator logon was recorded. |
| `2026-08-01 23:04:26–23:17:36` | Post-access activity | An external Administrator session was followed by Octo Browser installation, creation of a Defender exclusion, and outbound Octo-related traffic. |
| `2026-08-01–2026-08-11` | Recurring activity | Additional root-authenticated destructive MySQL sessions were observed from rotating external sources. |
| `2026-08-11 16:37:48` | Containment | `corp-dc01` was isolated through Microsoft Defender for Endpoint. |
<br><br>
<img width="1584" height="1181" alt="Microsoft Defender device timeline showing Octo Browser and PowerShell activity on corp-dc01" src="https://github.com/user-attachments/assets/a3264006-5844-4646-a57a-1c1b4a202a76" />

*Figure 21. Microsoft Defender device timeline showing the sequence of Octo Browser, PowerShell, file, and network activity associated with the investigated Administrator session. Portal timestamps are displayed in local time.*

<br><br>
<img width="1489" height="1169" alt="22" src="https://github.com/user-attachments/assets/310dde26-d1cc-4353-965d-3df3d06490f7" />
*Figure 22. MySQL destructive query activity. MySQL audit telemetry shows the kt_corp tables being enumerated and subsequently deleted, followed by creation of a RECOVER_YOUR_DATA table containing a Bitcoin ransom demand. The sequence demonstrates destructive database activity and extortion behavior identified during the incident investigation.*

<br><br>
<img width="1498" height="1087" alt="23" src="https://github.com/user-attachments/assets/8c425b3d-980a-4785-81eb-194f555ef82f" />
*Figure 23. Network activity investigation. Microsoft Defender network telemetry was reviewed to identify connections associated with the affected endpoint and correlate network activity with the MySQL compromise. Particular attention was given to connections involving the remote host 64.89.163.79 and MySQL service traffic observed during the destructive database activity.*

---
<a name="phase-8"></a>

### Phase 8: Containment

After confirming unauthorized endpoint and database activity, `CORP-DC01` was isolated using Microsoft Defender for Endpoint to prevent further attacker communication while preserving the system for forensic collection.

#### Actions Performed

- Isolated `CORP-DC01` through Microsoft Defender for Endpoint.
- Kept the VM powered on to preserve volatile and forensic evidence.
- Restored restrictive Azure Network Security Group rules.
- Removed public Internet access to MySQL port `3306`.
- Collected a post-isolation Microsoft Defender investigation package.
- Recorded the isolation and evidence-collection timestamps for timeline correlation.

**Isolation timestamp:** `2026-08-11T16:37:48Z`

<img width="1555" height="943" alt="isolation" src="https://github.com/user-attachments/assets/89dc17c9-b586-48e9-a1c2-232ca94b2177" />
*Figure 24. Endpoint isolation. The affected corp-dc01 endpoint was isolated using Microsoft Defender for Endpoint to restrict network communication and prevent additional remote activity while containment and remediation actions were performed.*

<br><br>
<img width="1643" height="418" alt="NSG" src="https://github.com/user-attachments/assets/d937a456-09d8-434d-b676-21de095d3fdf" />
*Figure 25. Network and MySQL service containment. The Azure Network Security Group was restored to its original restrictive configuration following the investigation. Permissive inbound access used during the controlled exposure was removed, including public access to the MySQL service on TCP port 3306, reducing the external attack surface and preventing additional unauthorized connections.*

<br><br>
#### Post-Isolation Evidence Collection

| Field | Value |
|---|---|
| Device | `CORP-DC01` |
| Isolation time | `2026-08-11T16:37:48Z` |
| Collection started | `2026-08-11T22:10:27Z` |
| Collection completed | `2026-08-11T22:10:52Z` |
| Time after isolation | Approximately 5 hours 33 minutes |
| Collection status | 80 of 80 collection actions completed |
| Package contents | 295 files |
| Package size | Approximately 19.3 MiB |
| Evidence collected | Processes, network connections, services, scheduled tasks, registry autoruns, installed programs, users and groups, prefetch files, temporary directories, and Windows Security events |

**Investigation-package SHA-256:**

`0cd9e713f45169e0e40e7e3bb078b42b71fa547c214162c4954d97d945560a89`

#### Post-Isolation Validation

| Evidence source | Observation | Assessment |
|---|---|---|
| Network connections | No established external RDP or MySQL sessions were present during collection. | Consistent with successful network containment. |
| Allowed connectivity | Only expected outbound HTTPS connections associated with Microsoft Defender and Windows services remained active. | Endpoint management and security telemetry remained available while isolated. |
| Listening services | MySQL ports `3306` and `33060`, along with RDP port `3389`, remained listening locally. | Isolation restricted communication but did not remediate the exposed services. |
| Process snapshot | No Octo Browser, OpenOffice, or interactive attacker shell was active during collection. MySQL remained running. | No known attacker tooling was active at that moment; this does not establish that the host was clean. |
| Residual artifacts | Octo Browser remained installed with an install date of August 1, 2026. OpenOffice remained installed with an install date of August 8, 2026. | Previously identified post-compromise software remained on the endpoint. |
| Execution evidence | BAM registry and prefetch artifacts recorded execution of Octo Browser, the OpenOffice installer, OpenOffice, PowerShell, and command-line utilities. | Corroborates endpoint activity identified during Phase 7. |
| Persistence review | No obvious unauthorized service, scheduled task, or startup Run key was identified in the collected snapshot. | Persistence was not confirmed through these mechanisms. |
| Accounts | `administrator` and `joedizon` remained members of the local Administrators group. | Privileged-account exposure still required remediation. |
| Active sessions | No interactive user or SMB session was active during collection. | No active remote user or SMB lateral-movement session was observed at capture time. |
| Evidence gap | The Windows Firewall log (`pfirewall.log`) was unavailable. | Host-firewall telemetry could not be validated from the package. |

#### Containment Assessment

The post-isolation investigation package contained no active external RDP, MySQL, or SMB session at the time of collection, which is consistent with successful network containment. However, MySQL and RDP remained listening, privileged accounts remained present, and previously identified software and execution artifacts were still stored on the endpoint.

Therefore, containment reduced the immediate risk but did not return the system to a trusted state. Removal of residual artifacts, account hardening, firewall restoration, MySQL hardening, and database recovery were carried forward into Phase 9.

---
<a name="phase-9"></a>

### Phase 9: Eradication and Recovery

Following containment, selected security controls were restored and the MySQL database was recovered for lab validation. Because the original VM was retained after confirmed external administrator access, the endpoint was not considered fully trusted.   
In a production environment, reimaging the host from a known-good source would provide greater assurance.

#### Eradication Actions
- Hardened the Administrator account
- Disabled the Guest account
- Secured exposed administrative credentials
- Run a full malware scan using Windows Defender
- Verified that unnecessary remote administrative access was disabled

#### Recovery Actions
- Re-enabled Windows Defender Firewall
- Restored the affected MySQL database from backup
- Verified that the expected databases and tables were available
- Confirmed legitimate access to the restored environment
- Validated that security controls remained enabled following recovery


<img width="1671" height="977" alt="firewall" src="https://github.com/user-attachments/assets/9e146856-c3cc-4791-9b35-2d28155a47d4" />
*Figure 26. Security controls restored following containment. All three Windows Defender Firewall profiles were enabled, and the built-in Guest account was disabled.*

<br><br>
<img width="1215" height="946" alt="antivirus" src="https://github.com/user-attachments/assets/9caa394f-639b-4e1b-a752-f5913d9e4010" />
*Figure 27. Post-containment full malware scan initiated on `corp-dc01` using Microsoft Defender Antivirus to identify potential malware and other suspicious artifacts.*

<br><br>
<img width="1395" height="1239" alt="sql" src="https://github.com/user-attachments/assets/23dd5a07-df18-4c05-9870-d72d81016ca7" />
*Figure 28. MySQL database recovery validation showing the restored `kt_corp` schema, expected application tables, successful table checks, and accessible sample records.*

---
<a name="phase-10"></a>

### Phase 10: Incident Reporting and Forensic Analysis

Following containment, eradication, and recovery, endpoint, network, authentication, and database telemetry were correlated to reconstruct the incident and document its scope, impact, and response.

The reporting package covers:

- End-to-end attack timeline
- Defender for Endpoint investigation
- Windows and MySQL authentication analysis
- MySQL query activity
- Network activity analysis
- Indicators of Compromise (IOCs)
- Host-level forensic comparison
- Containment and remediation actions
- Recovery validation
- Lessons learned and security recommendations

---
<a name="final-reports"></a>

#### 📄 Final Reports

##### Incident Response Report

The primary incident report presents the complete investigation of the exposed Azure environment. It correlates Microsoft Defender, Sentinel, Windows authentication, network, and MySQL telemetry to explain the attack sequence, affected resources, investigation findings, containment actions, recovery process, and recommended security improvements.

📄 **[View the Incident Response Report (PDF)](./assets/SecurityIncidentReport.pdf)**

##### Host DFIR Report

The Host Digital Forensics and Incident Response report provides a focused forensic examination of the affected Windows VM. It compares Microsoft Defender for Endpoint investigation packages collected at different stages of the incident and evaluates changes involving processes, persistence mechanisms, services, scheduled tasks, user accounts, network connections, event logs, and file-system artifacts.

Findings are classified as benign or suspicious and mapped to relevant MITRE ATT&CK tactics where supported by the evidence.

📄 **[View the Host DFIR Report (PDF)](./assets/HostDFIRReport.pdf)**

---
<a name="geographic-attack-analysis"></a>

#### 🌍 Geographic Attack Analysis

A Microsoft Sentinel Workbook and source-IP geolocation enrichment were used to visualize the approximate geographic origins of public authentication activity observed against the `corp-dc01` Windows VM and its MySQL service during the exposure period.

Larger bubbles represent locations associated with a higher number of recorded authentication events. Geographic coordinates are derived from source-IP registration data and should be interpreted as approximate network regions—not the physical locations of individual attackers.

<img width="1187" height="585" alt="map" src="https://github.com/user-attachments/assets/9530bee1-3d43-4948-970c-d44023c24889" />
*Figure 29. Microsoft Sentinel Workbook showing the approximate geographic distribution of public authentication activity observed against the Windows VM and MySQL service. Bubble size represents the number of recorded events associated with each location.*


---
<a name="skills-demonstrated"></a>

## 📈 Skills Demonstrated

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
<a name="key-takeaways"></a>

## 💡 Key Takeaways

This project demonstrates an end-to-end Security Operations Center (SOC) workflow. Rather than simply deploying a vulnerable system, the project emphasizes designing detections before exposure, collecting telemetry from multiple sources, investigating real attacker behavior, and performing containment and recovery using Microsoft Defender and Microsoft Sentinel.

The lab provided practical experience with cloud security monitoring, detection engineering, incident response, and threat hunting in a Microsoft security environment.

---
<a name="mitre-attack-mapping"></a>

## 📚 MITRE ATT&CK Mapping

| Tactic | Technique | Supporting Evidence |
|---|---|---|
| Credential Access | T1110 – Brute Force | Repeated failed authentication attempts and account lockouts |
| Initial Access | T1078.003 – Valid Accounts: Local Accounts | Successful external use of the local Administrator account |
| Initial Access | T1133 – External Remote Services | Confirmed external RemoteInteractive/RDP sessions |
| Discovery | T1082 – System Information Discovery | PowerShell queried `Win32_ComputerSystemProduct` for the system UUID |
| Collection | T1213.006 – Data from Information Repositories: Databases | `SELECT` activity against `kt_corp` tables |
| Defense Evasion | T1562.001 – Impair Defenses | Defender exclusion added for the Octo Browser directory |
| Impact | T1485 – Data Destruction | Confirmed `DROP TABLE` and `DROP DATABASE` operations |

[Back to top](#readme-top)

---
