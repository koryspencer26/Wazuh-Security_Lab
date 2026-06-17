# Wazuh-Security_Lab

## Project Overview
This project covers the end-to-end deployment of a home lab Security Operations Center (SOC) integrated with automated incident response (SOAR) capabilities. The setup captures live security telemetry from multiple Windows endpoints, streams logs to a centralized bare-metal Ubuntu SIEM via Wazuh, and will soon use containerized automation pipelines to simulate real-world threat detection and incident response via Shuffle.

---

## Technical Architecture & Data Flow
* **Telemetry Generation:** Wazuh agents monitor system, security, and application logs on endpoints in real time.
* **Log Forwarding:** Agents forward logs securely over designated UDP/TCP ports to a dedicated Ubuntu server on a static IP.
* **Aggregation & Alerting:** The Wazuh manager parses raw logs against detection rulesets and triggers alerts in the web dashboard.
* **SOAR Automation:** A containerized Shuffle SOAR stack connects via APIs to ingest alerts, run playbooks, and query open-source threat intelligence platforms.

---

## Environment Specifications
* **SIEM/SOAR Manager:** Ubuntu Server (Bare Metal)
* **Endpoint 001:** Windows 11 Workstation (Wazuh Agent)
* **Endpoint 002:** Windows 11 Laptop (Wazuh Agent)
* **Containers:** Docker Engine & Docker Compose
* **Automation:** Shuffle SOAR

---

## Project Phase Breakdown

### 📁 [Phase 1: Linux Networking & Physical Layer Hardening](./01-network-infrastructure/)
* **Objective:** Configure an Ubuntu Server baseline over a wireless network interface.
* **Troubleshooting & Fixes:** Discovered a routing loop and configuration issue caused by conflicting Netplan YAML blocks. Resolved this by isolating the specific hardware interface (`wlp2s0`) instead of relying on generic (`wlan0`) logic.
* **Security Hardening:** Restructured Netplan configuration file permissions to `chmod 600` to protect network configurations.

### 📁 [Phase 2: Transport Security & Firewall Optimization](./02-siem-deployment/)
* **Objective:** Establish secure communication channels between the Windows endpoints and the Ubuntu Server.
* **Troubleshooting & Fixes:** Diagnosed and fixed agent authentication dropouts (Error 1208) by auditing active listening ports on the host.
* **Security Hardening:** Configured a strict Uncomplicated Firewall (UFW) profile, opening only the exact ports needed for Wazuh registration (`tcp/<PORT>`) and log collection (`udp/<PORT>`), followed by a `systemd` restart to properly initialize the `authd` service.

### 📁 [Phase 3: Multi-Agent Onboarding & Telemetry Validation](./02-siem-deployment/)
* **Objective:** Scale the environment to support a distributed monitoring topology.
* **Key Hurdles Overcome:** Resolved endpoint service retry desynchronization by flushing local service state buffers via PowerShell.
* **Telemetry Verified:** Successfully registered multiple Windows 11 endpoints, establishing live telemetry streams and monitoring baseline user activity, system execution commands (`whoami`), and vulnerability indexing.

### 📁 [Phase 4: Containerized SOAR Integration (In Progress)](./03-soar-automation/)
* **Objective:** Deploy a microservices pipeline to act as an automated phishing analysis platform.
* **Current Focus:** Allocating Linux kernel virtual memory space (`vm.max_map_count`) to support back-end OpenSearch database clustering, pulling the Shuffle engine via Git, and configuring custom UFW routing for frontend delivery over port `<PORT>`.
