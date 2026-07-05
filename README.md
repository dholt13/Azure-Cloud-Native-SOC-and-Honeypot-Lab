# Azure Cloud-Native SOC and Honeypot Lab

This project simulates the implementation of a cloud-native Security Operations Center (SOC) using Microsoft Sentinel paired with an intentional Virtual Machine Honeypot deployed in Microsoft Azure. 

![Project Architecture](<img width="1541" height="797" alt="Screenshot 2026-07-05 at 3 17 55 PM" src="https://github.com/user-attachments/assets/ad439c1c-5adb-4b9e-b59a-cefd3d807489" />)

## Technology Utilized

* **Microsoft Azure Subscription** (Cloud Ecosystem Architecture)
* **Azure Virtual Networks & Network Security Groups** (Isolated Honeypot Environment)
* **Azure Virtual Machines** (Windows Server Deployment)
* **Log Analytics Workspace** (Centralized Kusto Query Language - KQL Repository)
* **Microsoft Sentinel** (Security Information and Event Management - SIEM)

## Table of Contents
1. [Azure Subscription Setup and Honeypot VM Deployment](#1-azure-subscription-setup-and-honeypot-vm-deployment)
2. [Configuring Log Analytics Workspace](#2-configuring-log-analytics-workspace)
3. [Forwarding Logs and Integrating with Microsoft Sentinel](#3-forwarding-logs-and-integrating-with-microsoft-sentinel)
4. [Building the Real-Time Attack Map Visualization](#5-building-the-real-time-attack-map-visualization)
5. [Creating Microsoft Sentinel Incidents and Alerts](#5-building-the-real-time-attack-map-visualization)
6. [Lab Summary and Operational Insights](#6-lab-summary-and-operational-insights)

---

### 1. Azure Subscription Setup and Honeypot VM Deployment
This phase focuses on establishing the underlying infrastructure within a dedicated Azure Resource Group. A Windows 10 Virtual Machine is provisioned to act as our exposed honeypot. To guarantee that external malicious actors can reach the VM, the inbound Network Security Group (NSG) rules are intentionally configured to allow **all** incoming traffic (`Any` protocol, `*` source port/IP) over RDP (Port 3389).

![VM Architecture](<img width="1436" height="982" alt="Screenshot 2026-07-04 at 9 48 42 PM" src="https://github.com/user-attachments/assets/3a202c48-6d06-4e47-8b21-91697661ec79" />)

### 2. Configuring Log Analytics Workspace
To gather raw telemetry data, a Log Analytics Workspace (LAW) is instantiated. Windows Event Viewer generates Security Log data (Event ID 4625 for failed login attempts) directly on the VM. In this step, the Azure VM is linked directly to the newly configured LAW, allowing a pipeline for log ingestion from the VM's OS straight into the Azure cloud analytics system.

![LAW Integration Placeholder](<img width="1701" height="957" alt="Screenshot 2026-07-04 at 10 24 17 PM" src="https://github.com/user-attachments/assets/cd9a32c1-2e20-417e-a497-bc7c28cd9079" />)

### 3. Forwarding Logs and Integrating with Microsoft Sentinel
With raw security telemetry feeding into the workspace, a Microsoft Sentinel is then layered on top of the workspace to transition the setup into a functional SIEM platform.

![Sentinel Deployment Placeholder](<img width="2015" height="996" alt="Screenshot 2026-07-05 at 3 53 10 PM" src="https://github.com/user-attachments/assets/ed573467-3555-4d7f-a8ec-17c4a0b0317a" />)

### 4. Building the Real-Time Attack Map Visualization
Below is the real-time threat intelligence map generated within Microsoft Sentinel, visualizing the geographic origins and frequency of inbound attacks targeting the honeypot instance.

![Attack Map Visualization](<img width="2016" height="992" alt="Screenshot 2026-07-05 at 7 27 31 AM" src="https://github.com/user-attachments/assets/acec0189-5849-4186-a3d0-c18feed2679a" />)

### 5. Configuring Microsoft Sentinel Analytics, Automation Rules, and Incidents
Following the implementation of the workbook visualization, custom Microsoft Sentinel Analytics and Automation Rules were created. To proactively flag malicious attackss, four specialized Scheduled Analytics Rules were configured within Microsoft Sentinel to monitor high-risk system actions and MITRE ATT&CK techniques.

The active alert rule matrix consists of the following parameters, as shown in Screenshot 2026-07-05 at 11.02.20 AM.jpg:

- Failed Logon Incidents (Severity: Medium): Detects persistent, brute-force authentication traffic attempting initial access.

- Anti-virus Malware Detection Incidents (Severity: Medium): Identifies signs of malicious code execution or localized endpoint compromise.

- User Account Created Incidents (Severity: Low): Tracks account creation events to verify persistence attempts by potential adversaries.

- Audit Log Incidents (Severity: High): Flags critical changes, defensive evasion, or unexpected manipulations within the system audit trail.

![Microsoft Sentinel Analytics](<img width="2009" height="961" alt="Screenshot 2026-07-05 at 11 02 20 AM" src="https://github.com/user-attachments/assets/59d15088-5ecd-42df-a2d0-60f9876cb2c2" />)

To maximize the operational efficiency of the home SOC, corresponding Automation Rules were engineered alongside the detection definitions. These orchestration rules trigger immediately upon incident creation to automate critical analyst triage workflows—such as dynamically routing tickets, updating incident statuses, and executing owner assignments (Assign to owner).

![Microsoft Sentinel Automation Rules](<img width="2009" height="961" alt="Screenshot 2026-07-05 at 11 02 57 AM" src="https://github.com/user-attachments/assets/e3beacf2-2a11-44da-a843-fae833d97895" />)

When an incoming attack matches the analytical logic, Microsoft Sentinel elevates the log telemetry to an active security incident. As illustrated below, the operational triage dashboard consolidates crucial evidence—including total event counts, mapped MITRE ATT&CK tactics (e.g., Initial Access), and targeted entity profiles—providing an optimized workbench for root-cause analysis and threat containment.

![Microsoft Sentinel Incidents](<img width="2012" height="988" alt="Screenshot 2026-07-05 at 11 06 20 AM" src="https://github.com/user-attachments/assets/d8dd33a5-830b-442d-a6c9-88bd16678681" />)

### 6. Lab Summary and Operational Insights

This hands-on project successfully simulated the end-to-end operational lifecycle of an enterprise-grade Security Operations Center (SOC) within a cloud-native ecosystem. By intentionally exposing a Windows 10 Virtual Machine to the public internet, the lab provided immediate, definitive proof of the velocity and scale of modern automated threat vectors.

#### Key Operational Insights

* **The Reality of Direct Internet Exposure:** By opening the Network Security Group (NSG) to allow unrestricted inbound traffic over RDP (Port 3389), the virtual machine was discovered by global automated botnets and malicious scanners. This emphasized the critical real-world need for strict perimeter controls like zero-trust firewall configurations.
* **Centralized Data Telemetry Ingestion:** The project demonstrated the foundational importance of centralized logging. Forwarding raw Windows Event Viewer logs into a Log Analytics Workspace proved how separate compute infrastructure can be effectively chained to a centralized security repository for continuous inspection.
* **Proactive SIEM Triage over Reactive Defense:** Rather than manually sorting through overwhelming text files, layering Microsoft Sentinel on top of the ingest pipeline shifted operations from reactive log management to proactive cyber defense. The interactive Sentinel Workbook provided immediate geographical threat context, demonstrating how complex data can be structured into high-impact visual threat intelligence.
* **Automated Orchestration and Incident Lifecycle:** The engineering of specific Analytics Rules (covering brute-force initial access, malware execution, persistence via user creation, and audit trail defense evasion) showcased how a modern SOC scales. Coupled with automation rules that handle owner assignment and incident classification, the environment proved that automated orchestration significantly lowers a security analyst's Mean Time to Acknowledge (MTTA) and Mean Time to Respond (MTTR).

#### Final Conclusion

This project successfully bridging the gap between security engineering (building log pipelines, writing analytic logic, and establishing alert criteria) and security operations (triaging live incidents, monitoring attack dashboards, and analyzing adversary behavior). Ultimately, the lab highlights Microsoft Sentinel's powerful capability to turn raw, unformatted network noise into an orchestrated, highly visible, and defensive security posture.
