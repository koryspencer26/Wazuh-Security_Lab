# Phase 3: Multi-Agent Deployment & Telemetry Validation
## Objective: 
Scale the lab environment to support multi-node monitoring.

---

## Endpoint Onboarding & Scaling


### Step 1: Manual Agent Authentication (Windows 11)
With the server listening, I opened an elevated PowerShell prompt on the Windows endpoint to run the authentication binary and trigger a secure key exchange with the manager:

```
# Ran as Admin in Windows PowerShell
& "C:\Program Files (x86)\ossec-agent\agent-auth.exe" -m <SERVER_IP>
```
Result: ```INFO: Valid key received.```

### Step 2: Multi-Agent Deployment
To scale the environment into a multi-node architecture, a second Windows device was onboarded using the same pipeline. To verify both agents were actively streaming telemetry side-by-side, I queried the manager's agent database via the CLI:
```
# List all registered and active agents on the server
sudo /var/ossec/bin/agent_control -l
```
### Status Verification
* Agent 001 (Primary Device): Active
* Agent 002 (Secondary Device): Active
* Telemetry Verification: Confirmed end-to-end log ingestion by executing standard discovery commands (whoami) on the endpoints and filtering for the events in the SIEM manager

---

## 4. Key Takeaways
* Device Tracking: Isolating endpoint events by unique cryptographic IDs (``001, 002``) allows for clean data segregation, making it easier to track potential lateral movement across the network.
