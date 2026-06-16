# Wazuh-Security_Lab

## Project Overview
This project demonstrates the end-to-end deployment of a hardened, bare-metal Security Operations Center (SOC) ecosystem integrated with Security Orchestration, Automation, and Response (SOAR) capabilities. The architecture captures live security telemetry from multiple endpoints, streams logs securely to a centralized Linux-based SIEM, and leverages containerized automation pipelines to simulate an enterprise-grade threat detection and incident response environment.

---

## Technical Architecture & Flow
1. **Endpoint Telemetry Generation:** Continuous monitoring of system, security, and application logs on endpoints via specialized host agents.
2. **Secure Transport Layer:** Logs are forwarded over custom UDP/TCP streams to a dedicated infrastructure manager operating on a hardened static IP network.
3. **Log Aggregation & Rule Indexing:** The centralized SIEM engine parses raw telemetry against predefined threat detection rulesets, outputting alerts to a web management console.
4. **SOAR Orchestration:** A containerized automation stack hooks into the environment via APIs and sockets to process security events, execute playbooks, and integrate with open-source threat intelligence platforms.

---

## Environment Specifications
* **Infrastructure Host (SIEM/SOAR Manager):** Ubuntu Server (Bare Metal Architecture)
* **Endpoint 001:** Windows 11 Workspace Device (Wazuh Agent Active)
* **Endpoint 002:** Windows 11 Secondary Device (Wazuh Agent Active)
* **Containerization Engine:** Docker Engine & Docker Compose
* **Automation Platform:** Shuffle SOAR

---

## Project Phase Breakdown

### 📁 [Phase 1: Linux Networking & Physical Layer Hardening](./01-network-infrastructure/)
* **Objective:** Establish a rock-solid, headless server network baseline over wireless architecture.
* **Key Hurdles Overcome:** Remediation of configuration drift and "split-brain" routing loops caused by competing Netplan YAML blocks. Isolated specific hardware interfaces (`wlp2s0`) over standard generic logic (`wlan0`).
* **Security Control implemented:** Hardened configuration file permissions to `chmod 600` to prevent credential exposure.

### 📁 [Phase 2: Transport Security & Firewall Optimization](./02-siem-deployment/)
* **Objective:** Open secure communication pipelines between Windows endpoints and the Ubuntu core manager.
* **Key Hurdles Overcome:** Diagnosed and bypassed agent-auth network dropouts (Error 1208) by auditing active network sockets.
* **Security Control Implemented:** Implemented a restricted Uncomplicated Firewall (UFW) profile, strictly opening incoming traffic to ports `<PORT>/udp` (events) and `<PORT>/tcp` (enrollment), followed by a systemd cycle to initialize the `authd` registration service.

### 📁 [Phase 3: Multi-Agent Onboarding & Telemetry Validation](./02-siem-deployment/)
* **Objective:** Scale the environment to support a distributed monitoring topology.
* **Key Hurdles Overcome:** Resolved endpoint service retry desynchronization by flushing local service state buffers via PowerShell.
* **Telemetry Verified:** Successfully registered multiple Windows 11 endpoints, establishing live telemetry streams and monitoring baseline user activity, system execution commands (`whoami`), and vulnerability indexing.

### 📁 [Phase 4: Containerized SOAR Integration (In Progress)](./03-soar-automation/)
* **Objective:** Deploy a microservices pipeline to act as an automated phishing analysis platform.
* **Current Focus:** Allocating Linux kernel virtual memory space (`vm.max_map_count`) to support back-end OpenSearch database clustering, pulling the Shuffle engine via Git, and configuring custom UFW routing for frontend delivery over port `<PORT>`.
