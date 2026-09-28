# Cisco Basic LAN Configuration using Packet Tracer

## Project Overview

This project demonstrates a basic **Local Area Network (LAN)** created using **Cisco Packet Tracer**.

The network consists of two PCs connected through a **Cisco Catalyst 3560 switch**. The project focuses on basic IP addressing, switch configuration, management access, and connectivity testing.

---

## Objectives

* Create a basic LAN topology
* Configure IPv4 addresses on PCs
* Configure a Cisco switch using CLI
* Configure switch management IP
* Configure console and VTY passwords
* Configure switch access ports
* Test connectivity between devices using `ping`

---

## Network Topology

**PC1 → Cisco 3560 Switch → PC2**

### Devices Used

* 2 × PCs
* 1 × Cisco Catalyst 3560-24PS Switch
* Copper straight-through Ethernet cables

---

## IP Addressing

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| PC1 | FastEthernet0 | 192.168.1.1 | 255.255.255.0 |
| PC2 | FastEthernet0 | 192.168.1.2 | 255.255.255.0 |
| SW1 | VLAN 1 | 192.168.1.10 | 255.255.255.0 |

---

## Switch Configuration

The Cisco switch was configured using the CLI.

### Basic Configuration

```text
enable
configure terminal
hostname SW1
enable secret class
```

### Console Configuration

```text
line console 0
password cisco
login
exit
```

### VTY Configuration

```text
line vty 0 15
password cisco
login
exit
```

### Management IP Configuration

```text
interface vlan 1
ip address 192.168.1.10 255.255.255.0
no shutdown
exit
```

### Access Port Configuration

```text
interface range fastethernet 0/1 - 2
switchport mode access
no shutdown
exit
```

### Save Configuration

```text
end
copy running-config startup-config
```

---

## Connectivity Testing

Connectivity was tested using the `ping` command.

### PC1 to PC2

```text
ping 192.168.1.2
```

### PC2 to PC1

```text
ping 192.168.1.1
```

Successful replies confirmed that both PCs can communicate through the switch.

---

## Verification Commands

The following command was used to verify the switch interfaces:

```text
show ip interface brief
```

---

## Project Files

* `basic-lan-network.pkt` — Cisco Packet Tracer project
* `topology.JPG` — Network topology screenshot
* `ping-test.JPG` — Connectivity test screenshot

---

## Learning Outcomes

Through this project, I practiced:

* Basic LAN configuration
* IPv4 addressing
* Cisco IOS CLI commands
* Switch management
* Access port configuration
* Basic network troubleshooting
* Connectivity testing using `ping`

---

## Future Improvements

* Configure DHCP
* Create VLANs
* Configure inter-VLAN routing
* Add a router
* Connect multiple switches
* Implement basic network security

---

**Tool:** Cisco Packet Tracer  
**Project Level:** Beginner  
**Focus:** Networking Fundamentals
