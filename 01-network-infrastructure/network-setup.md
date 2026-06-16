# Phase 1: Linux Networking & Physical Layer Hardening

## Objective
Establish a headless, highly stable Ubuntu Server deployment over a physical wireless architecture to serve as the static core manager for all incoming SIEM and SOAR data streams.

---

## 1. Technical Obstacle: "The Question Mark" & Configuration Drift
Upon initial headless deployment, the infrastructure experienced total network dropout, represented by a generic connectivity failure status symbol. 

### Root Cause Analysis (RCA)
1. **Predictable Network Interface Mismatch:** The Linux kernel did not enumerate the wireless hardware link as a generic `wlan0` interface. 
2. **YAML Configuration Drift:** The `/etc/netplan/` directory contained duplicate and competing configurations. The server was experiencing a "split-brain" state between `NetworkManager` (typically used for graphical interfaces) and `networkd` (the standard backend system for headless servers), causing routing loops and configuration rejection.

---

## 2. Resolution & Remediation Steps

### Step 1: Hardware Interface Isolation
To map the exact physical wireless card, the link layer was queried to discover the true kernel assignment.
```bash
ip link show
