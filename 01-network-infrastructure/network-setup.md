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
```

### Step 2: Netplan Reconciliation & Static Allocation
The duplicate configuration blocks were purged, and a single, unified declarative network state was authored using strict YAML syntax (specifically ensuring a 6-space indentation rule for the downstream DNS nameservers array to prevent compiler validation errors).
```bash
YAML

network:
  version: 2
  renderer: networkd
  wifis:
    wlp2s0:
      dhcp4: no
      addresses: [<SERVER_IP>/SUBNET]
      routes:
        - to: default
          via: <GATEWAY>
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
      access-points:
        "YOUR_HOME_SSID":
          password: "SANITIZED_WIFI_PASSWORD"
```

### Step 3: Security Hardening (Access Control)
Because Netplan configuration files contain plain-text network keys, leaving the file readable by non-root users represents a critical security risk. The file permissions were audited and locked down using an absolute permission scheme.
```bash
# Restrict read/write permissions exclusively to the root user
sudo chmod 600 /etc/netplan/*.yaml

# Generate internal configuration files and apply the state machine
sudo netplan generate
sudo netplan apply
```

---

## 3. Verification & Metrics
To confirm successful remediation, the network stack was validated by verifying the local sockets, routing table configuration, and link state.
* Command: ```ip addr show wlp2s0``` -> Confirmed binding to static IP ```<SERVER_IP>```.
* Command: ```ping -c 3 8.8.8.8``` -> Confirmed 0% packet loss, verifying successful Layer 3 gateway routing.

---

## 4. Key Takeaways for the Enterprise
* Layer 1-3 Synchronization: Hardware interfaces must be explicitly mapped before network configurations are pushed to production.
* Least Privilege File Security: Configuration files holding infrastructure secrets must explicitly restrict read capabilities ```(chmod 600)``` to mitigate internal privilege escalation vectors.



