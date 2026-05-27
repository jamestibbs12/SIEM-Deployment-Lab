#: Splunk Enterprise Deployment & Attack Simulation

## Project Overview
This project demonstrates the deployment of a centralized Security Information and Event Management (SIEM) pipeline using **Splunk Enterprise**. The objective was to architect a cloud-based lab environment, securely onboard logs from both Windows and Linux endpoints, simulate malicious credential attacks, and build operational security telemetry via custom alert logic and real-time visualization dashboards.

[ Host Machine CLI / RDP ]
                          │ (Attack Simulation & Mgmt)
                          ▼
┌─────────────────────── Cloud Virtual Network ───────────────────────┐
│                                                                     │
│   ┌─────────────────┐       ┌─────────────────┐      ┌───────────┐  │
│   │    Target-VM    │       │  Windows-Client │      │  Splunk-  │  │
│   │   (Linux CLI)   │       │   (Windows)     │      │  Server   │  │
│   │                 │       │                 │      │           │  │
│   │  ┌───────────┐  │       │  ┌───────────┐  │      │ ┌───────┐ │  │
│   │  │ Universal │  │       │  │ Universal │  │      │ │Splunk │ │  │
│   │  │ Forwarder │  │       │  │ Forwarder │  │      │ │Core   │ │  │
│   └──└───┬───────┘──┘       └──└───┬───────┘──┘      └─└▲──────┘─┘  │
│          │                         │                    │           │
│          └─────────────────────────┴────────────────────┘           │
│                       Syslog & WinEventLog (Port 9997)              │
└─────────────────────────────────────────────────────────────────────┘

## Technical Capabilities Demonstrated
* **Architecture & Networking:** Cloud VPC configuration, firewall rule definitions, private IP routing, and secure remote management (SSH/RDP).
* **SIEM Engineering:** Index management, source type mapping, Splunk Universal Forwarder orchestration, and deployment topology.
* **Attack & Pen Testing:** Brute force automation, credential spraying simulation, and log generation.
* **Defensive Operations:** Search Processing Language (SPL) optimization, real-time threshold alerting, and executive dashboard design.

---

## Lab Architecture & Step-by-Step Implementation

### Phase 1: Virtual Network & Infrastructure Provisioning
* Engineered a dedicated cloud virtual network to act as the isolated testing boundary.
* Provisioned three distinct virtual machines with static private addressing:
  * **Splunk-Server:** Dedicated Linux-based instance hosting the SIEM core.
  * **Target-VM:** Ubuntu Linux server simulating a corporate web or application server.
  * **Windows-Client:** Windows instance simulating a standard enterprise workstation.
* Configured network security groups (NSGs) to allow internal communication across necessary telemetry ports (e.g., TCP `9997` for Splunk indexing) while restricting exposed public management ports.

### Phase 2: SIEM Core Deployment & Configuration
* Established an SSH session into the **Splunk-Server** to download, extract, and install the Splunk Enterprise binary.
* Initialized the Splunk daemon, configured boot-start, and opened local firewall ports to allow web interface traffic.
* Connected to the Splunk Web UI via the host machine's browser to build the underlying architecture, setting up a listening port on `9997` to ingest incoming forwarder traffic.

### Phase 3: Linux Endpoint Onboarding & Telemetry Validation
* Accessed **Target-VM** via SSH to install the lightweight **Splunk Universal Forwarder**.
* Configured `outputs.conf` to establish a persistent logging channel to the Splunk Indexer over port `9997`.
* Mapped `inputs.conf` to track and ingest Linux system security logs (`/var/log/auth.log` or `/var/log/secure`).
* **Attack Simulation:** Executed brute force and credential spraying attacks against the Linux endpoint from the host CLI to generate high-fidelity authentication failure telemetry.

### Phase 4: Windows Endpoint Onboarding & Telemetry Validation
* Authenticated to **Windows-Client** via Remote Desktop Protocol (RDP).
* Deployed the Windows variant of the Splunk Universal Forwarder, configuring it to capture the `Security`, `System`, and `Application` Windows Event Log channels.
* **Attack Simulation:** Leveraged the local Windows command-line environment to systematically script and simulate failed login thresholds, ensuring Windows Event ID `4625` (An account failed to log on) was explicitly tripped and recorded.

### Phase 5: Detection Engineering & Alert Rules
* Wrote targeted Search Processing Language (SPL) queries to filter and aggregate log data across both divergent operating systems.
* Engineered an automated alerting rule evaluating telemetry in real-time. 
* **Logic Threshold:** Trigger an operational alert if **5 or more failed login attempts** occur within a rolling 5-minute window for a specific user account or originating IP address.

---

## SIEM Telemetry & Dashboards

A custom, production-ready Splunk Dashboard was built to unify the disparate log pipelines into a single pane of glass. The dashboard tracks security posture updates in real time using optimized SPL components:

### 1. Failed Login Attempts (Unified)
Aggregates both Linux `sshd` failure events and Windows Event ID `4625` to provide a running timeline of authentication anomalies.

### 2. Top Attacking IP Addresses
Parses originating source IPs from failed authentication arrays and visualizes them into a high-visibility chart, pinpointing the most aggressive threat vectors.

### 3. Top 10 Process Executions (Windows/Linux)
Monitors endpoint hygiene by tracking process creation events (e.g., Windows Event ID `4688` / Linux audit logs) to detect potential post-exploitation tools, living-off-the-land techniques, or unauthorized binaries.

---

## Tools & Technologies Used
* **SIEM:** Splunk Enterprise, Splunk Universal Forwarder (Linux/Windows)
* **OS Platforms:** Ubuntu Linux, Windows 10/Server
* **Networking/Protocols:** VPC/VNet, SSH, RDP, TCP port `9997`, TCP port `8000`
* **Security & Testing Tools:** Custom CLI scripts, Brute Force / Credential Spraying tools
* **Languages:** Search Processing Language (SPL), Bash, PowerShell
