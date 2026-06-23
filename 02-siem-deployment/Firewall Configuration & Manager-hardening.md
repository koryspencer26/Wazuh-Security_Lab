# Phase 2: Agent Transport Security & SIEM Manager Hardening

## Objective
Configure secure communication between Windows 11 endpoints and the centralized Wazuh manager on Ubuntu Server, ensuring the host securely accepts agent enrollment and telemetry streams while blocking unauthorized traffic.

---

## 1. Troubleshooting Agent-Auth Failure (Error 1208)
During the deployment of the first Windows 11 endpoint, the agent failed to connect to the manager and threw the following error: 
`Error 1208 - Unable to connect to password/enrollment service`. 

### Root Cause Analysis (RCA)
1. **Host Firewall Blocking Traffic:** The Ubuntu host was running a default "deny all incoming" firewall policy, dropping incoming packets from the Windows agent at the network boundary.
2. **Service Daemon Binding Issue:** Following the network changes in Phase 1, the Wazuh enrollment daemon (`authd`) was not actively listening on its designated network sockets.

---

## 2. Resolution & Remediation Steps

### Step 1: Configure the Firewall (UFW)
To allow agent telemetry and enrollment without exposing unnecessary host services, UFW was configured to allow specific ports dedicated to SIEM traffic.

```bash
# Allow agent telemetry ingestion (UDP)
sudo ufw allow <PORT>/udp

# Allow secure agent enrollment/handshake (TCP)
sudo ufw allow <PORT>/tcp

# Reload firewall to apply changes
sudo ufw reload

# Verify active firewall rules
sudo ufw status verbose
```
### Step 2: Audit Active Sockets & Listeners
To ensure the manager was properly hosting the enrollment service after the firewall changes, I checked the active socket listeners:

```
# Verify the manager is listening on the required TCP/UDP ports
sudo ss -tlpn | grep -E '<PORT>|<PORT>'
```

### Step 3: Restart Services & Validate
To force the manager to bind its listeners to the newly stabilized static IP (<SERVER_IP>), I restarted the SIEM service:

```
# Restart the core SIEM manager system service
sudo systemctl restart wazuh-manager
```
Verification: Re-running ```sudo ss -tlpn | grep <tcp PORT>``` confirmed the process was successfully binding to the socket and waiting for a connection.

---

## 4. Key Takeaways
* Firewall vs. Service Visibility: Network troubleshooting requires checking both the Firewall rules and the Socket listeners via ``ss``. A port can be open on a firewall, but if a service isn't actively listening, traffic will still drop.







