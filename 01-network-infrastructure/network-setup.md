# Phase 1: Networking & Server Setup

## Objective
Deploy a stable, headless Ubuntu Server over Wi-Fi to serve as the centralized manager host for all incoming SIEM and SOAR data streams.

---

## 1. Troubleshooting Headless Network Dropout
Immediately after the initial headless installation, the server completely lost network connectivity and became unreachable.

### Root Cause Analysis (RCA)
1. **Predictable Network Interface Name:** The Linux kernel did not label the wireless hardware as a generic `wlan0` interface. Instead, it used a predictable name based on the physical hardware slot.
2. **Netplan Configuration Conflict:** The `/etc/netplan/` directory contained duplicate, competing configuration files. The server was caught in a conflict between `NetworkManager` (typically used for GUI desktops) and `systemd-networkd` (the standard backend for headless servers), causing routing loops and configuration rejection.

---

## 2. Resolution & Remediation Steps

### Step 1: Identify the Wireless Interface
To map the exact name assigned to the physical wireless card, I queried the link layer:
```
ip link show
```

### Step 2: Clean Up Netplan & Assign a Static IP
I purged the duplicate configuration blocks and created a single, clean Netplan file targeted at ``systemd-networkd``.

Note on YAML Syntax: Netplan is incredibly strict with spacing. Ensure a consistent 6-space indentation for the downstream DNS ``addresses`` array to prevent parsing and validation errors.
```
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

### Step 3: File Permission Hardening
Because Netplan configuration files contain plain-text Wi-Fi passwords, leaving the file readable by non-root users is a severe security risk. I restricted the file permissions exclusively to root:
```bash
# Restrict read/write permissions to the root user only
sudo chmod 600 /etc/netplan/*.yaml

# Generate and apply the new network configuration
sudo netplan generate
sudo netplan apply
```

---

## 3. Verification & Connectivity Metrics
To confirm the server was stable and routing traffic correctly, I validated the interface binding and outbound network path:
* Check Interface Binding: ``ip addr show wlp2s0`` confirmed the system successfully bound to the static IP ``<SERVER_IP>``.
* Test External Routing: ``ping -c 3 8.8.8.8`` returned a 0% packet loss, confirming proper Layer 3 gateway routing.

---

## 4. Key Takeaways
* Explicit Interface Mapping: Modern Linux kernels rarely default to ``wlan0`` or ``eth0``. Always verify the kernel's predictable device names (``ip link``) before writing network configuration scripts.
* Credential Security in Configs: Any configuration files that store cleartext credentials or infrastructure secrets must be locked down immediately to ``600`` permissions to protect against local privilege escalation.



