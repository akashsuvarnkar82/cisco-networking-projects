# DHCP Network Configuration using Cisco Packet Tracer

## Project Overview

This project demonstrates the configuration of a basic network using **DHCP (Dynamic Host Configuration Protocol)** in Cisco Packet Tracer.

A Cisco router is configured as a DHCP server to automatically assign IP addresses, subnet masks, default gateways, and DNS server information to multiple PCs connected through a switch.

The project also includes network connectivity testing and DHCP troubleshooting.

---

## Objectives

* Configure a Cisco router interface
* Configure a Cisco router as a DHCP server
* Automatically assign IP addresses to PCs
* Configure the default gateway and DNS server
* Configure DHCP excluded addresses
* Verify DHCP leases
* Test network connectivity using `ping`
* Troubleshoot an incorrect DHCP configuration
* Practice Cisco IOS CLI commands

---

## Network Topology

```text
                  Router R1
              G0/0: 192.168.10.1
                       |
                       |
                    Fa0/1
                     SW1
                  /   |   \
                 /    |    \
               PC1   PC2   PC3
```

---

## Devices Used

* 1 × Cisco 2911 Router
* 1 × Cisco 2960 Switch
* 3 × PCs
* Copper Straight-Through Ethernet Cables

---

## IP Addressing

| Device | Interface | IP Address    | Subnet Mask   | Configuration |
| ------ | --------- | ------------- | ------------- | ------------- |
| R1     | G0/0      | 192.168.10.1  | 255.255.255.0 | Static        |
| PC1    | Fa0       | 192.168.10.11 | 255.255.255.0 | DHCP          |
| PC2    | Fa0       | 192.168.10.12 | 255.255.255.0 | DHCP          |
| PC3    | Fa0       | 192.168.10.13 | 255.255.255.0 | DHCP          |

---

## Router Configuration

### Basic Configuration

```text
enable
configure terminal
hostname R1
```

### Configure Router Interface

```text
interface gigabitethernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
```

### Purpose

* `enable` — enters privileged EXEC mode
* `configure terminal` — enters global configuration mode
* `hostname R1` — changes the router hostname
* `interface gigabitethernet 0/0` — selects the router interface
* `ip address` — assigns an IPv4 address
* `no shutdown` — enables the interface
* `exit` — exits the current configuration mode

The router interface `192.168.10.1` acts as the **default gateway** for the PCs.

---

## DHCP Configuration

### Configure Excluded Addresses

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

This prevents DHCP from assigning addresses from:

```text
192.168.10.1 - 192.168.10.10
```

The remaining addresses can be assigned to DHCP clients.

### Create DHCP Pool

```text
ip dhcp pool LAN
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit
```

### Command Details

* `ip dhcp pool LAN` — creates a DHCP pool named LAN
* `network` — defines the network from which IP addresses are assigned
* `default-router` — provides the default gateway to DHCP clients
* `dns-server` — provides the DNS server to DHCP clients
* `exit` — exits DHCP configuration mode

---

## Switch Configuration

The switch connects the router and PCs.

### Basic Configuration

```text
enable
configure terminal
hostname SW1
```

### Configure Access Ports

```text
interface range fastethernet 0/1 - 4
switchport mode access
no shutdown
exit
```

### Command Details

* `interface range` — selects multiple interfaces
* `switchport mode access` — configures the ports as access ports
* `no shutdown` — enables the ports

---

## PC Configuration

Each PC was configured to obtain its network settings automatically.

Go to:

**PC → Desktop → IP Configuration → DHCP**

The PCs successfully received IP addresses from the router.

```text
PC1 → 192.168.10.11
PC2 → 192.168.10.12
PC3 → 192.168.10.13
```

---

## DHCP Verification

### Check DHCP Bindings

```text
show ip dhcp binding
```

This command displays the IP addresses assigned to DHCP clients.

Example:

```text
192.168.10.11    Automatic
192.168.10.12    Automatic
192.168.10.13    Automatic
```

### Check DHCP Pool

```text
show ip dhcp pool
```

This command displays:

* DHCP pool information
* Total addresses
* Leased addresses
* Excluded addresses
* DHCP network range

---

## Interface Verification

```text
show ip interface brief
```

This command displays the interface name, IP address, status, and protocol status.

Example:

```text
Interface              IP-Address      Status
GigabitEthernet0/0     192.168.10.1    up
```

---

## Configuration Verification

### Display Running Configuration

```text
show running-config
```

This displays the current active configuration of the router.

### Display DHCP Configuration

```text
show running-config | section dhcp
```

This displays only the DHCP-related configuration.

---

## PC Network Commands

### Check IP Configuration

```text
ipconfig
```

### Display Detailed IP Configuration

```text
ipconfig /all
```

This displays:

* IPv4 address
* Subnet mask
* Default gateway
* DHCP server
* DNS server
* MAC address

### Release DHCP Address

```text
ipconfig /release
```

This releases the current DHCP address.

### Renew DHCP Address

```text
ipconfig /renew
```

This requests a new IP address from the DHCP server.

---

## Connectivity Testing

### PC1 → Router

```text
ping 192.168.10.1
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

### PC1 → PC2

```text
ping 192.168.10.12
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

### PC1 → PC3

```text
ping 192.168.10.13
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

Successful replies confirmed connectivity between the devices.

---

## DHCP Troubleshooting

During testing, PC1 initially received:

```text
192.168.10.2
```

instead of an address from the intended DHCP range:

```text
192.168.10.11 - 192.168.10.254
```

### Problem

The DHCP excluded-address command contained an IP address typo:

```text
ip dhcp excluded-address 192.168.10.1 190.168.10.10
```

The second address should have been:

```text
192.168.10.10
```

### Correction

The incorrect configuration was removed:

```text
no ip dhcp excluded-address 192.168.10.1 190.168.10.10
```

The correct configuration was applied:

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

The PC DHCP lease was then renewed:

```text
ipconfig /release
ipconfig /renew
```

After correction, the PC received an IP address from the intended DHCP range.

---

## Complete DHCP Configuration

```text
enable
configure terminal

hostname R1

interface gigabitethernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp pool LAN
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit

end
```

---

## Commands Used

```text
enable
configure terminal
hostname
interface
interface range
ip address
no shutdown
shutdown
exit
end
ip dhcp excluded-address
ip dhcp pool
network
default-router
dns-server
show ip interface brief
show ip dhcp binding
show ip dhcp pool
show running-config
show running-config | section dhcp
ping
ipconfig
ipconfig /all
ipconfig /release
ipconfig /renew
```

---

## Key Concepts Learned

* DHCP
* Dynamic IP Address Assignment
* DHCP Pool
* DHCP Excluded Addresses
* IPv4 Addressing
* Subnet Mask
* Default Gateway
* DNS
* Router Interface Configuration
* Switch Access Ports
* Cisco IOS CLI
* Network Connectivity Testing
* DHCP Troubleshooting

---

## Project Files

* `project-2-dhcp-network.pkt` — Cisco Packet Tracer project
* `README.md` — Project documentation
* `topology.png` — Network topology
* `dhcp-binding.png` — DHCP lease verification
* `pc1-ipconfig.png` — PC IP configuration
* `ping-test.png` — Connectivity testing

---

## Learning Outcome

This project provided practical experience in configuring a Cisco router as a DHCP server and troubleshooting IP address assignment in a small LAN.

The complete workflow was:

**Router Configuration → DHCP Configuration → Automatic IP Assignment → Verification → Connectivity Testing → Troubleshooting**

---

## Future Improvements

* Configure VLANs
* Configure trunking
* Configure inter-VLAN routing
* Add multiple networks
* Configure static routing
* Add Internet connectivity
* Implement basic network security

---

**Tool:** Cisco Packet Tracer
**Project:** 02 - DHCP Network Configuration
**Level:** Beginner
**Focus:** DHCP, IP Addressing and Network Troubleshooting
