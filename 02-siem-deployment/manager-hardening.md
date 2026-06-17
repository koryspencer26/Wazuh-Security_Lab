# Phase 2: Transport Security & SIEM Manager Hardening

## Objective
Configure secure communication pipelines between distributed Windows 11 endpoints and the centralized Ubuntu core manager, ensuring the infrastructure host securely accepts agent enrollment and telemetry streams while denying unauthorized traffic.

---

## 1. Technical Obstacle: Agent-Auth Dropout (Error 1208)
During the initial deployment of the first Windows 11 endpoint, the agent failed to connect to the manager, throwing a critical communication fault: `Error 1208 - Unable to connect to password/enrollment service`. 

### Root Cause Analysis (RCA)
1. **Network Ingress Disconnect (Firewall Dropping Packets):** The Ubuntu infrastructure host was running a default "deny all incoming" firewall policy. The transport packets sent by the Windows agent were being dropped at the host boundary.
2. **Service Daemon Desynchronization:** Due to previous Phase 1 network restructuring, the internal Wazuh enrollment daemon (`authd`) was not actively binding to or listening on its designated network sockets.

---

## 2. Resolution & Remediation Steps

### Step 1: Firewall Optimization (UFW)
To allow secure telemetry ingestion without exposing unnecessary host services, the Uncomplicated Firewall (UFW) was configured to open specific "valves" dedicated to SIEM traffic.

```bash
# Explicitly permit agent telemetry ingestion (Layer 4 UDP)
sudo ufw allow <PORT>/udp

# Explicitly permit secure agent enrollment/handshaking (Layer 4 TCP)
sudo ufw allow <PORT>/tcp

# Reload the firewall engine to apply changes
sudo ufw reload

# Validate the active firewall policy profile
sudo ufw status verbose
```

### Step 2. Sockets & Listener Auditing
To verify that the operating system was correctly hosting the enrollment service after the firewall adjustment, the network utility layer was queried to inspect active socket listeners.

```
# Check if the manager is actively listening on both ports, tcp|udp
sudo ss -tlpn | grep -E '<PORT>|<PORT>'
```

### Step 3: Initialization & Handshake Validation






