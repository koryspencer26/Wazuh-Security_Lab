# Wazuh_Security_Lab

## Project Overview
This project details the end-to-end deployment of a home security lab, setting up a centralized Security Operations Center (SOC) with automated incident response (SOAR) capabilities. The environment captures live security telemetry from multiple Windows endpoints, streams logs to a dedicated bare-metal Ubuntu Server running a Wazuh SIEM manager, and uses containerized automation playbooks via Shuffle SOAR to simulate real-world threat detection and incident response.

---

## Technical Architecture & Data Flow
*   **Telemetry Generation:** Wazuh agents monitor Windows event logs, sysmon data, and application activity on endpoints in real time.
*   **Log Forwarding:** Agents forward logs securely over designated UDP/TCP ports to the centralized Ubuntu server.
*   **Aggregation & Alerting:** The Wazuh manager parses raw logs against detection rules, indexing security events and generating alerts in the web dashboard.
*   **SOAR Automation:** A containerized Shuffle SOAR stack connects via webhook APIs to ingest alerts, run automated playbooks, and query threat intelligence platforms.

---

## Environment Specifications
*   **SIEM/SOAR Manager:** Ubuntu Server (Bare Metal)
*   **Monitored Endpoints:** Windows 11 Workstations (Wazuh Agents)
*   **Containerization:** Docker Engine & Docker Compose
*   **Automation Engine:** Shuffle SOAR

---

## Project Phase Breakdown

### 📁 [Phase 1: Networking & Server Setup](./01-network-infrastructure/)
*   **Objective:** Deploy and configure a headless Ubuntu Server baseline over a physical wireless network interface.
*   **Troubleshooting & Fixes:** Resolved a severe network dropout and routing conflict caused by overlapping Netplan configurations. Identified the exact predictable hardware interface name (`wlp2s0`) to replace the default `wlan0` logic.
*   **Security Hardening:** Restricted Netplan configuration file permissions (`chmod 600`) to secure local network and credential data.

### 📁 [Phase 2: Transport Security & Firewall Hardening](./02-siem-deployment/)
*   **Objective:** Establish secure communication pipelines between Windows endpoints and the Ubuntu host.
*   **Troubleshooting & Fixes:** Fixed agent enrollment failures (`Error 1208`) by auditing active socket listeners using `ss` and identifying service binding issues on the manager.
*   **Security Hardening:** Configured strict Uncomplicated Firewall (UFW) rules, opening only the exact ports required for Wazuh registration (TCP) and telemetry ingestion (UDP).

### 📁 [Phase 3: Multi-Agent Deployment & Telemetry Validation](./03-siem-deployment/)
*   **Objective:** Scale the lab environment to support multi-node monitoring.
*   **Troubleshooting & Fixes:** Resolved endpoint communication retry loops by restarting stalled agent services and flushing configurations via administrative PowerShell.
*   **Telemetry Verification:** Successfully onboarded multiple Windows devices and verified live log ingestion by executing discovery commands (`whoami`) on endpoints and monitoring the corresponding SIEM alerts.

### 📁 [Phase 4: Wazuh Rule Tuning (In Progress)](./04-soar-automation/)

### 📁 [Phase 5: Containerized SOAR Integration (In Progress)](./05-soar-automation/)
*   **Objective:** Deploy and configure Shuffle SOAR inside Docker to automate phishing and alert analysis.
*   **Current Focus:** Tuning Linux kernel virtual memory parameters (`vm.max_map_count`) to support the backend OpenSearch database cluster, staging the Shuffle environment via Docker Compose, and adjusting host firewall routing for web access.
