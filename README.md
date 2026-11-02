# Secure Campus Network Using Cisco Packet Tracer

> **Mini Project** – Router Configuration • Switch Security • SSH • Port Security • Console/Enable Security • Password Encryption

## Table of Contents

 1. [Project Objective](#1-project-objective)
 2. [Network Scenario](#2-network-scenario)
 3. [Devices Required](#3-devices-required)
 4. [Network Topology](#4-network-topology)
 5. [IP Addressing Plan](#5-ip-addressing-plan)
 6. [Physical Connections](#6-physical-connections)
 7. [Configuration Tasks](#7-configuration-tasks)
 8. [Testing & Verification](#8-testing--verification)
 9. [Final Verification Checklist](#9-final-verification-checklist)
10. [Project Deliverables](#10-project-deliverables)
11. [Conclusion](#11-conclusion)

---

## 1. Project Objective

Design and implement a small secure campus network in Cisco Packet Tracer. The project introduces basic Cisco device configuration and security mechanisms, including:

- Router and switch configuration
- Console and privileged EXEC security
- SSH remote access
- Switch port security
- Password encryption

## 2. Network Scenario

A small university department has **one router, two Cisco switches, and six PCs**. The router connects two IP networks. The switches provide LAN connectivity and are secured against unauthorized access and unauthorized devices.

## 3. Devices Required

| Qty | Device | Name |
|-----|--------|------|
| 1 | Cisco 2911 Router | R1 |
| 2 | Cisco 2960 Switches | S1, S2 |
| 6 | PCs | PC1 – PC6 |
| — | Copper straight-through cables | — |

## 4. Network Topology

```
                     +-------------+
                     |     R1      |
                     |   Router    |
                     +------+------+
                      G0/0  |  G0/1
              +-------------+-------------+
              |                           |
        +-----+-----+               +-----+-----+
        |     S1    |               |     S2    |
        +-----+-----+               +-----+-----+
        |     |     |               |     |     |
       PC1   PC2   PC3             PC4   PC5   PC6
```

- **Network 1:** `192.168.10.0/24` (S1 side)
- **Network 2:** `192.168.20.0/24` (S2 side)

## 5. IP Addressing Plan

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | — |
| R1 | G0/1 | 192.168.20.1 | 255.255.255.0 | — |
| S1 | VLAN 1 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 |
| S2 | VLAN 1 | 192.168.20.2 | 255.255.255.0 | 192.168.20.1 |
| PC1 | NIC | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 | NIC | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC3 | NIC | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC4 | NIC | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC5 | NIC | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| PC6 | NIC | 192.168.20.12 | 255.255.255.0 | 192.168.20.1 |

## 6. Physical Connections

| From | To |
|------|----|
| R1 G0/0 | S1 G0/1 |
| R1 G0/1 | S2 G0/1 |
| S1 Fa0/1 | PC1 |
| S1 Fa0/2 | PC2 |
| S1 Fa0/3 | PC3 |
| S2 Fa0/1 | PC4 |
| S2 Fa0/2 | PC5 |
| S2 Fa0/3 | PC6 |

---

## 7. Configuration Tasks

### Task 1 – Configure Router R1

```
enable
! Enters privileged EXEC mode.

configure terminal
! Enters global configuration mode.

hostname R1
! Sets the hostname of the router to R1.

interface gigabitEthernet 0/0
! Enters configuration mode for interface GigabitEthernet 0/0.

 ip address 192.168.10.1 255.255.255.0
 ! Assigns IP address 192.168.10.1 with subnet mask 255.255.255.0 to the interface.

 no shutdown
 ! Enables and powers on the interface.

 exit
 ! Exits interface configuration mode back to global configuration mode.

interface gigabitEthernet 0/1
! Enters configuration mode for interface GigabitEthernet 0/1.

 ip address 192.168.20.1 255.255.255.0
 ! Assigns IP address 192.168.20.1 with subnet mask 255.255.255.0 to the interface.

 no shutdown
 ! Enables and powers on the interface.

 exit
 ! Exits interface configuration mode back to global configuration mode.

show ip interface brief
! Displays a summary status and IP addresses of all router interfaces.
```

### Task 2 – Secure Router Console and Enable Mode

```
line console 0
! Enters console line configuration mode.

 password Console@123
 ! Sets the password for console access.

 login
 ! Enables password authentication prompt for console access.

 exit
 ! Exits console line configuration mode.

enable secret Enable@123
! Encrypts and sets the password required to access privileged EXEC mode.
```

The console password protects console access. The `enable secret` protects privileged EXEC mode (`R1#`). Using `enable secret` is preferred over a plain `enable password`.

### Task 3 – Password Encryption

```
service password-encryption
! Encrypts plain-text passwords stored in the configuration file.

show running-config
! Displays the active running configuration file.
```

> **Note:** `service password-encryption` causes line passwords in the device configuration to be stored in an obfuscated form. It is **not** equivalent to strong cryptographic protection; use `enable secret` for privileged access.

### Task 4 – Configure SSH on R1

```
ip domain-name campus.local
! Sets the IP domain name required for generating RSA keys.

username admin privilege 15 secret Admin@123
! Creates a user account with full administrator privileges (level 15) and an encrypted password.

crypto key generate rsa
! Generates RSA key pairs required for enabling SSH encryption.
! Enter 1024 when prompted (or 2048 if supported)

ip ssh version 2
! Forces the device to use SSH version 2 for enhanced security.

line vty 0 4
! Enters virtual terminal (VTY) lines configuration mode for remote management.

 login local
 ! Configures VTY lines to use the local user database for authentication.

 transport input ssh
 ! Restricts incoming remote connections on VTY lines to SSH only.

 exit
 ! Exits VTY configuration mode.

show ip ssh
! Displays current status and settings of SSH on the device.
```

### Task 5 – Configure Switch S1

```
enable
! Enters privileged EXEC mode.

configure terminal
! Enters global configuration mode.

hostname S1
! Sets the hostname of the switch to S1.

enable secret Enable@123
! Encrypts and sets the password required to access privileged EXEC mode.

service password-encryption
! Encrypts plain-text passwords stored in the configuration file.

interface vlan 1
! Enters configuration mode for Management VLAN 1.

 ip address 192.168.10.2 255.255.255.0
 ! Assigns management IP address 192.168.10.2 to VLAN 1.

 no shutdown
 ! Enables and powers on the VLAN 1 interface.

 exit
 ! Exits interface configuration mode.

ip default-gateway 192.168.10.1
! Sets the default gateway IP address for remote switch management.
```

### Task 6 – Secure S1 Console and Configure SSH

```
line console 0
! Enters console line configuration mode.

 password SwitchConsole@123
 ! Sets the password for console access.

 login
 ! Enables password authentication prompt for console access.

 exit
 ! Exits console line configuration mode.

ip domain-name campus.local
! Sets the IP domain name required for RSA key generation.

username admin privilege 15 secret Admin@123
! Creates a local administrator account with privilege level 15.

crypto key generate rsa
! Generates RSA key pairs required for enabling SSH encryption.
! Enter 1024 when prompted (or 2048 if supported)

ip ssh version 2
! Configures the switch to run SSH version 2.

line vty 0 4
! Enters virtual terminal (VTY) lines configuration mode.

 login local
 ! Configures VTY lines to authenticate users against the local username database.

 transport input ssh
 ! Permits SSH access only and blocks unencrypted Telnet connections.

 exit
 ! Exits VTY configuration mode.
```

### Task 7 – Configure Port Security on S1

```
interface fastEthernet 0/1
! Enters configuration mode for interface FastEthernet 0/1.

 switchport mode access
 ! Sets the port access mode to connect to end devices.

 switchport port-security
 ! Enables port security feature on the interface.

 switchport port-security maximum 1
 ! Limits the interface to allow only 1 MAC address.

 switchport port-security mac-address sticky
 ! Dynamically learns connected MAC address and saves it to running-config.

 switchport port-security violation shutdown
 ! Configures the port to shut down if an unauthorized MAC address connects.

 exit
 ! Exits interface configuration mode.

interface fastEthernet 0/2
! Enters configuration mode for interface FastEthernet 0/2.

 switchport mode access
 ! Sets the port access mode to connect to end devices.

 switchport port-security
 ! Enables port security feature on the interface.

 switchport port-security maximum 1
 ! Limits the interface to allow only 1 MAC address.

 switchport port-security mac-address sticky
 ! Dynamically learns connected MAC address and saves it to running-config.

 switchport port-security violation shutdown
 ! Configures the port to shut down if an unauthorized MAC address connects.

 exit
 ! Exits interface configuration mode.

interface fastEthernet 0/3
! Enters configuration mode for interface FastEthernet 0/3.

 switchport mode access
 ! Sets the port access mode to connect to end devices.

 switchport port-security
 ! Enables port security feature on the interface.

 switchport port-security maximum 1
 ! Limits the interface to allow only 1 MAC address.

 switchport port-security mac-address sticky
 ! Dynamically learns connected MAC address and saves it to running-config.

 switchport port-security violation shutdown
 ! Configures the port to shut down if an unauthorized MAC address connects.

 exit
 ! Exits interface configuration mode.
```

### Task 8 – Configure Switch S2

```
enable
! Enters privileged EXEC mode.

configure terminal
! Enters global configuration mode.

hostname S2
! Sets the hostname of the switch to S2.

enable secret Enable@123
! Encrypts and sets the password required to access privileged EXEC mode.

service password-encryption
! Encrypts plain-text passwords stored in the configuration file.

interface vlan 1
! Enters configuration mode for Management VLAN 1.

 ip address 192.168.20.2 255.255.255.0
 ! Assigns management IP address 192.168.20.2 to VLAN 1.

 no shutdown
 ! Enables and powers on the VLAN 1 interface.

 exit
 ! Exits interface configuration mode.

ip default-gateway 192.168.20.1
! Sets the default gateway IP address for remote switch management.

line console 0
! Enters console line configuration mode.

 password SwitchConsole@123
 ! Sets the password for console access.

 login
 ! Enables password authentication prompt for console access.

 exit
 ! Exits console line configuration mode.

ip domain-name campus.local
! Sets the IP domain name required for RSA key generation.

username admin privilege 15 secret Admin@123
! Creates a local administrator account with privilege level 15.

crypto key generate rsa
! Generates RSA key pairs required for SSH operation.
! Enter 1024 when prompted (or 2048 if supported)

ip ssh version 2
! Forces the switch to use SSH version 2.

line vty 0 4
! Enters virtual terminal (VTY) lines configuration mode.

 login local
 ! Sets authentication to use local database credentials.

 transport input ssh
 ! Restricts incoming management connections to SSH only.

 exit
 ! Exits VTY configuration mode.
```

### Task 9 – Configure Port Security on S2

```
interface range fastEthernet 0/1-3
! Enters range configuration mode for interfaces FastEthernet 0/1 through 0/3.

 switchport mode access
 ! Sets all selected ports to access mode for end hosts.

 switchport port-security
 ! Enables port security on all selected interfaces.

 switchport port-security maximum 1
 ! Restricts each port to allow only 1 MAC address.

 switchport port-security mac-address sticky
 ! Learns connected MAC addresses automatically and binds them to the configuration.

 switchport port-security violation shutdown
 ! Sets port security action to shut down interface upon unauthorized MAC detection.

 exit
 ! Exits interface range configuration mode.
```

### Task 10 – Configure the PCs

On each PC, go to **Desktop → IP Configuration** and apply:

| PC | IP Address | Subnet Mask | Gateway |
|----|------------|-------------|---------|
| PC1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC3 | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC4 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC5 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| PC6 | 192.168.20.12 | 255.255.255.0 | 192.168.20.1 |

---

## 8. Testing & Verification

### Task 11 – Test Network Connectivity

From the **PC1 Command Prompt**:

```
ping 192.168.10.1
! Tests network connectivity to default gateway R1 interface G0/0.

ping 192.168.20.10
! Tests ICMP end-to-end connectivity to PC4.

ping 192.168.20.11
! Tests ICMP end-to-end connectivity to PC5.

ping 192.168.20.12
! Tests ICMP end-to-end connectivity to PC6.
```

**Expected result:** successful replies after ARP/MAC learning has completed (the first ping may time out).

### Task 12 – Test SSH Access

**From PC1:**

```
ssh -l admin 192.168.10.2
! Initiates an encrypted SSH connection to Switch S1 using username admin.
Password: Admin@123

ssh -l admin 192.168.10.1
! Initiates an encrypted SSH connection to Router R1 using username admin.
Password: Admin@123
```

**From PC4:**

```
ssh -l admin 192.168.20.2
! Initiates an encrypted SSH connection to Switch S2 using username admin.
Password: Admin@123
```

### Task 13 – Verify Port Security

```
show port-security
! Displays summary port security information for all security-enabled ports on the switch.

show port-security address
! Displays all secure MAC addresses learned or configured on all interfaces.

show port-security interface fastEthernet 0/1
! Displays detailed port security settings and current status for interface FastEthernet 0/1.

show mac address-table
! Displays the active MAC address table of the switch.
```

Verify that:

- Port security is **enabled**
- Maximum secure MAC address count is **1**
- **Sticky** MAC learning is active
- Violation mode is **shutdown**

### Task 14 – Demonstrate a Port Security Violation

1. Connect PC1 to S1 Fa0/1 and generate traffic (e.g., ping the router). The switch learns PC1's MAC address.
2. Disconnect PC1 and connect a different PC to Fa0/1.
3. Because only one secure MAC address is permitted, the switch should detect a violation.

**Check the port:**

```
show port-security interface fastEthernet 0/1
! Displays port security state and violation status on interface FastEthernet 0/1.
```

**Recover the interface:**

```
configure terminal
! Enters global configuration mode.

interface fastEthernet 0/1
! Enters configuration mode for interface FastEthernet 0/1.

 shutdown
 ! Manually disables the interface to clear the err-disabled state.

 no shutdown
 ! Enables and restarts the interface after resolving the security breach.

 exit
 ! Exits interface configuration mode.
```

### Task 15 – Disable Unused Switch Ports

Run on **S1 and S2**:

```
interface range fastEthernet 0/4-24
! Selects range of unused switch ports FastEthernet 0/4 through 0/24.

 shutdown
 ! Disables selected unused ports to prevent unauthorized physical connections.

 exit
 ! Exits interface range configuration mode.
```

This reduces the risk of unauthorized devices being connected to unused active ports.

### Task 16 – Save the Configuration

Run on **R1, S1 and S2**:

```
copy running-config startup-config
! Saves active running configuration changes to NVRAM startup configuration.
```

---

## 9. Final Verification Checklist

| Test | Expected Result | Status |
|------|-----------------|--------|
| PC1 → Router ping | Successful | Passed |
| PC1 → PC4 ping | Successful | Passed |
| SSH → R1 | Successful | Passed |
| SSH → S1 | Successful | Passed |
| SSH → S2 | Successful | Passed |
| Port security | Enabled | Passed |
| Sticky MAC learning | Successful | Passed |
| Unauthorized MAC test | Violation detected | Passed |
| Unused ports | Shutdown | Passed |

---

## 10. Project Deliverables

- [ ] **Packet Tracer Model:** Executable `.pkt` network topology file containing saved `startup-config` states across all managed network devices.
- [ ] **Architecture Blueprint:** High-resolution diagram illustrating physical/logical network design, host bindings, interface mapping, and security boundaries.
- [ ] **IP & VTP/VLAN Addressing Schema:** Complete network specification matrix mapping IP ranges, default gateways, subnet masks, interface bindings, and management access paths.
- [ ] **Device Configurations:** Standardized Cisco IOS startup configuration text files for router `R1` and switches `S1`, `S2`.
- [ ] **Verification & Test Suite:** Audit log documenting ICMP reachability, SSH v2 authentication sessions, MAC binding tables, violation triggering, and error-recovery procedures.
- [ ] **Network Hardening & Security Audit Report:** Technical summary highlighting AAA control implementations, encrypted administrative access, MAC-level access control, and attack surface minimization tactics.

---

## 11. Conclusion

This project provides practical experience in securing Cisco routers and switches. Students configure basic IP connectivity, protect console and privileged EXEC access, enable SSH for secure remote administration, encrypt locally configured passwords, and use switch port security to restrict unauthorized devices. The completed Packet Tracer topology demonstrates how multiple basic security controls can be combined to improve the security of a small campus network.
