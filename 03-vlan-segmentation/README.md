# VLAN Configuration and Network Segmentation using Cisco Packet Tracer

## Project Overview

This project demonstrates the configuration of **VLANs (Virtual Local Area Networks)** and basic network segmentation using Cisco Packet Tracer.

A Cisco 2960 switch is configured with two separate VLANs representing different departments: **IT** and **HR**.

The project demonstrates how VLANs logically separate devices on the same physical switch and how devices within the same VLAN can communicate while devices in different VLANs cannot communicate without Layer 3 routing.

---

## Objectives

* Create VLANs on a Cisco switch
* Configure VLAN names
* Assign switch ports to specific VLANs
* Configure access ports
* Assign static IP addresses to PCs
* Verify VLAN configuration
* Test connectivity within the same VLAN
* Test communication between different VLANs
* Perform basic VLAN troubleshooting
* Practice Cisco IOS CLI commands

---

## Network Topology

```text
                           SW1
         ┌──────────┬───────┴────┬──────────┐
       Fa0/1      Fa0/2        Fa0/3      Fa0/4
         │          │            │          │
        PC1        PC2          PC3        PC4
         IT         IT           HR         HR
      VLAN 10    VLAN 10      VLAN 20    VLAN 20
```

---

## Devices Used

* 1 × Cisco 2960 Switch
* 4 × PCs
* Copper Straight-Through Ethernet Cables

---

## VLAN Configuration

| VLAN    | Name | Department    | Ports        |
| ------- | ---- | ------------- | ------------ |
| VLAN 10 | IT   | IT Department | Fa0/1, Fa0/2 |
| VLAN 20 | HR   | HR Department | Fa0/3, Fa0/4 |

---

## IP Addressing

| Device | VLAN    | IP Address    | Subnet Mask   |
| ------ | ------- | ------------- | ------------- |
| PC1    | VLAN 10 | 192.168.10.11 | 255.255.255.0 |
| PC2    | VLAN 10 | 192.168.10.12 | 255.255.255.0 |
| PC3    | VLAN 20 | 192.168.20.11 | 255.255.255.0 |
| PC4    | VLAN 20 | 192.168.20.12 | 255.255.255.0 |

No default gateway is configured because **inter-VLAN routing is not configured in this project**.

---

## Switch Configuration

### Basic Configuration

```text
enable
configure terminal
hostname SW1
```

---

## Create VLANs

### Create VLAN 10

```text
vlan 10
name IT
exit
```

### Create VLAN 20

```text
vlan 20
name HR
exit
```

---

## Configure VLAN 10 Ports

PC1 and PC2 are connected to Fa0/1 and Fa0/2.

```text
interface range fastethernet 0/1 - 2
switchport mode access
switchport access vlan 10
no shutdown
exit
```

### Command Details

* `interface range` — selects multiple interfaces
* `switchport mode access` — configures the ports as access ports
* `switchport access vlan 10` — assigns the ports to VLAN 10
* `no shutdown` — enables the interfaces

---

## Configure VLAN 20 Ports

PC3 and PC4 are connected to Fa0/3 and Fa0/4.

```text
interface range fastethernet 0/3 - 4
switchport mode access
switchport access vlan 20
no shutdown
exit
```

### Command Details

* `interface range` — selects multiple interfaces
* `switchport mode access` — configures the ports as access ports
* `switchport access vlan 20` — assigns the ports to VLAN 20
* `no shutdown` — enables the interfaces

---

## Complete Switch Configuration

```text
enable
configure terminal

hostname SW1

vlan 10
name IT
exit

vlan 20
name HR
exit

interface range fastethernet 0/1 - 2
switchport mode access
switchport access vlan 10
no shutdown
exit

interface range fastethernet 0/3 - 4
switchport mode access
switchport access vlan 20
no shutdown
exit

end
```

---

## VLAN Verification

The following command was used to verify the VLAN configuration:

```text
show vlan brief
```

Expected result:

```text
VLAN  Name    Status    Ports

10    IT      active    Fa0/1, Fa0/2
20    HR      active    Fa0/3, Fa0/4
```

This confirms that the correct switch ports are assigned to each VLAN.

---

## Interface Verification

The following command was used to check the status of the switch ports:

```text
show interfaces status
```

Expected result for the connected ports:

```text
Port    Status       VLAN

Fa0/1   connected    10
Fa0/2   connected    10
Fa0/3   connected    20
Fa0/4   connected    20
```

All four ports were successfully connected.

---

## Connectivity Testing

### PC1 → PC2

PC1 and PC2 belong to VLAN 10.

```text
ping 192.168.10.12
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

**Result: Successful**

---

### PC3 → PC4

PC3 and PC4 belong to VLAN 20.

```text
ping 192.168.20.12
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

**Result: Successful**

---

### PC1 → PC3

PC1 belongs to VLAN 10 and PC3 belongs to VLAN 20.

```text
ping 192.168.20.11
```

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4
```

**Result: Failed as expected**

---

### PC3 → PC2

PC3 belongs to VLAN 20 and PC2 belongs to VLAN 10.

```text
ping 192.168.10.12
```

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4
```

**Result: Failed as expected**

---

## Why Does Inter-VLAN Communication Fail?

VLAN 10 and VLAN 20 are separate Layer 2 networks.

```text
VLAN 10                         VLAN 20

PC1 ─── PC2                    PC3 ─── PC4
 IT                              HR
192.168.10.0/24               192.168.20.0/24
```

The switch can forward traffic within the same VLAN, but it cannot route traffic between VLAN 10 and VLAN 20.

A **Layer 3 device**, such as a router or multilayer switch, is required for communication between the VLANs.

Inter-VLAN routing will be implemented in the next project.

---

## VLAN Troubleshooting

A troubleshooting scenario was performed to verify whether PC3 and PC4 could communicate.

### Step 1 — Check VLAN Assignment

```text
show vlan brief
```

Result:

```text
Fa0/3 → VLAN 20
Fa0/4 → VLAN 20
```

Both ports were correctly assigned to VLAN 20.

### Step 2 — Check Port Status

```text
show interfaces status
```

Result:

```text
Fa0/3 → connected → VLAN 20
Fa0/4 → connected → VLAN 20
```

Both ports were physically connected and operational.

### Step 3 — Test Connectivity

From PC3:

```text
ping 192.168.20.12
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

PC3 and PC4 successfully communicated.

### Troubleshooting Conclusion

The VLAN configuration and switch ports were working correctly.

---

## Useful Commands

```text
enable
configure terminal
hostname
vlan
name
interface
interface range
switchport mode access
switchport access vlan
no shutdown
shutdown
exit
end
show vlan brief
show interfaces status
show interfaces
show running-config
show mac address-table
ping
```

---

## Key Concepts Learned

* VLAN
* Network Segmentation
* VLAN 10
* VLAN 20
* Access Ports
* Layer 2 Switching
* Broadcast Domains
* IPv4 Addressing
* Cisco IOS CLI
* VLAN Verification
* Network Connectivity Testing
* Basic Network Troubleshooting

---

## Project Files

* `project-3-vlan-network.pkt` — Cisco Packet Tracer project
* `README.md` — Project documentation
* `topology.png` — Network topology
* `vlan-configuration.png` — VLAN verification
* `vlan-connectivity-test.png` — Connectivity testing

---

## Learning Outcome

This project provided practical experience in creating and configuring VLANs on a Cisco switch.

The complete workflow was:

**Create VLANs → Assign Ports → Configure IP Addresses → Verify VLANs → Test Connectivity → Troubleshoot**

The project demonstrated that devices in the same VLAN can communicate through Layer 2 switching, while communication between different VLANs requires Layer 3 routing.

---

## Future Improvements

* Configure trunking
* Configure router-on-a-stick
* Configure inter-VLAN routing
* Add DHCP for each VLAN
* Add multiple switches
* Configure VLAN security
* Implement ACLs

---

**Tool:** Cisco Packet Tracer
**Project:** 03 - VLAN Configuration and Network Segmentation
**Level:** Beginner
**Focus:** VLANs, Switching and Network Segmentation
