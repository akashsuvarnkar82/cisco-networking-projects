# Inter-VLAN Routing using Router-on-a-Stick

## 📌 Project Overview

This project demonstrates **Inter-VLAN Routing** using the **Router-on-a-Stick** method in Cisco Packet Tracer.

Two separate VLANs are configured on a Cisco switch:

- **VLAN 10 – IT**
- **VLAN 20 – HR**

A Cisco 2911 router is connected to the switch using a **trunk link**. Router subinterfaces are configured to allow communication between the two VLANs.

---

## 🎯 Objectives

- Configure VLAN 10 and VLAN 20
- Configure access ports for different departments
- Configure a trunk link between switch and router
- Configure router subinterfaces
- Configure 802.1Q VLAN encapsulation
- Configure default gateways for PCs
- Enable communication between different VLANs
- Verify and troubleshoot the network

---

## 🖥️ Network Topology

```text
                    Cisco 2911
                       R1
                     G0/0
                       |
                    TRUNK
                       |
                    Fa0/24
                     SW1
              Cisco 2960 Switch
                 /          \
             VLAN 10       VLAN 20
                |              |
            IT Department   HR Department
             /     \          /     \
           PC1     PC2      PC3     PC4
```

---

## 🔧 Devices Used

| Device   | Model               | Purpose                      |
| -------- | ------------------- | ---------------------------- |
| Router   | Cisco 2911          | Inter-VLAN Routing           |
| Switch   | Cisco 2960          | VLAN and trunk configuration |
| PC       | PC-PT               | End devices                  |
| Software | Cisco Packet Tracer | Network simulation           |

---

## 🌐 VLAN Configuration

| VLAN    | Department | Ports        |
| ------- | ---------- | ------------ |
| VLAN 10 | IT         | Fa0/1, Fa0/2 |
| VLAN 20 | HR         | Fa0/3, Fa0/4 |

---

## 📡 IP Addressing

| Device     | IP Address    | Subnet Mask   | Default Gateway |
| ---------- | ------------- | ------------- | --------------- |
| PC1 – IT   | 192.168.10.11 | 255.255.255.0 | 192.168.10.1    |
| PC2 – IT   | 192.168.10.12 | 255.255.255.0 | 192.168.10.1    |
| PC3 – HR   | 192.168.20.11 | 255.255.255.0 | 192.168.20.1    |
| PC4 – HR   | 192.168.20.12 | 255.255.255.0 | 192.168.20.1    |
| R1 G0/0.10 | 192.168.10.1  | 255.255.255.0 | —               |
| R1 G0/0.20 | 192.168.20.1  | 255.255.255.0 | —               |

---

# ⚙️ Configuration

## 1. Configure VLANs on SW1

```text
enable
configure terminal

vlan 10
name IT
exit

vlan 20
name HR
exit
```

---

## 2. Configure IT Access Ports

```text
interface range fastethernet 0/1 - 2
switchport mode access
switchport access vlan 10
no shutdown
exit
```

---

## 3. Configure HR Access Ports

```text
interface range fastethernet 0/3 - 4
switchport mode access
switchport access vlan 20
no shutdown
exit
```

---

## 4. Configure Trunk Port

Router R1 is connected to **SW1 Fa0/24**.

```text
interface fastethernet 0/24
switchport mode trunk
no shutdown
exit
```

Verify:

```text
show interfaces trunk
```

---

# 🔀 Router Configuration

## 5. Enable Router Interface

```text
enable
configure terminal

interface gigabitethernet 0/0
no shutdown
exit
```

The physical G0/0 interface does not receive a normal IP address because routing is performed using subinterfaces.

---

## 6. Configure VLAN 10 Subinterface

```text
interface gigabitethernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
```

This interface acts as the default gateway for **VLAN 10**.

---

## 7. Configure VLAN 20 Subinterface

```text
interface gigabitethernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
```

This interface acts as the default gateway for **VLAN 20**.

---

# 💻 PC Configuration

### PC1 – IT

```text
IP Address:      192.168.10.11
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

### PC2 – IT

```text
IP Address:      192.168.10.12
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

### PC3 – HR

```text
IP Address:      192.168.20.11
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.20.1
```

### PC4 – HR

```text
IP Address:      192.168.20.12
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.20.1
```

---

# 🔍 Verification

## Verify VLANs

On SW1:

```text
show vlan brief
```

Expected:

```text
VLAN 10
Fa0/1
Fa0/2

VLAN 20
Fa0/3
Fa0/4
```

---

## Verify Trunk

```text
show interfaces trunk
```

Fa0/24 should appear as a trunk port.

---

## Verify Router Interfaces

On R1:

```text
show ip interface brief
```

Expected:

```text
GigabitEthernet0/0       up
GigabitEthernet0/0.10    192.168.10.1
GigabitEthernet0/0.20    192.168.20.1
```

---

## Verify Routing Table

```text
show ip route
```

Expected connected networks:

```text
C    192.168.10.0/24
C    192.168.20.0/24
```

---

# 🧪 Connectivity Testing

## Same VLAN Testing

PC1 → PC2:

```text
ping 192.168.10.12
```

Expected:

```text
Success
```

PC3 → PC4:

```text
ping 192.168.20.12
```

Expected:

```text
Success
```

---

## Inter-VLAN Testing

PC1 → PC3:

```text
ping 192.168.20.11
```

Expected:

```text
Success
```

PC3 → PC1:

```text
ping 192.168.10.11
```

Expected:

```text
Success
```

This confirms that the router is performing **Inter-VLAN Routing**.

---

# 🛠️ Troubleshooting

During testing, the router subinterfaces were correctly configured:

```text
G0/0.10 → 192.168.10.1/24
G0/0.20 → 192.168.20.1/24
```

The PCs also had the correct IP addresses and default gateways.

However, connectivity to the router gateway was timing out.

The investigation showed that:

```text
SW1 Fa0/24 → Down
```

Since Fa0/24 was the connection between SW1 and R1, the trunk link could not carry VLAN traffic to the router.

### Troubleshooting Commands

On SW1:

```text
show interfaces status
show interfaces trunk
show interfaces fa0/24 switchport
```

On R1:

```text
show ip interface brief
show ip route
```

### Corrective Configuration

```text
interface fastethernet 0/24
switchport mode trunk
no shutdown
```

Also verify that the physical cable is connected:

```text
R1 G0/0 ↔ SW1 Fa0/24
```

using a **Copper Straight-Through** cable.

---

# 🧠 How Router-on-a-Stick Works

The router uses a single physical interface with multiple subinterfaces.

```text
             R1
            G0/0
              |
           802.1Q
            TRUNK
              |
             SW1
          /       \
     VLAN 10     VLAN 20
       IT          HR
```

### Traffic Flow

For example, when PC1 communicates with PC3:

```text
PC1
192.168.10.11
     ↓
Default Gateway
192.168.10.1
     ↓
R1 G0/0.10
     ↓
Router routes traffic
     ↓
R1 G0/0.20
     ↓
SW1 VLAN 20
     ↓
PC3
192.168.20.11
```

The router receives traffic from VLAN 10 and routes it toward VLAN 20.

---

# 📋 Important Commands

### Switch

```text
show vlan brief
```

Displays VLANs and assigned ports.

```text
show interfaces trunk
```

Displays trunk ports and allowed VLANs.

```text
show interfaces status
```

Displays port status and VLAN assignment.

```text
show interfaces fa0/24 switchport
```

Displays detailed switchport configuration.

### Router

```text
show ip interface brief
```

Displays interface and subinterface status.

```text
show ip route
```

Displays the router's routing table.

---

# 📚 What I Learned

- VLAN segmentation
- Access ports
- Trunk ports
- 802.1Q encapsulation
- Router subinterfaces
- Router-on-a-Stick
- Default gateways
- Inter-VLAN Routing
- Routing table verification
- Network troubleshooting
- Physical link troubleshooting
- Cisco IOS verification commands

---

# 🚀 Future Improvements

- Configure DHCP for both VLANs
- Add a second switch
- Configure VLANs across multiple switches
- Implement inter-VLAN routing using a Layer 3 switch
- Add ACLs between IT and HR
- Configure redundant links
- Build the same topology in GNS3

---

# 📁 Project Files

```text
Project-04-Router-on-a-Stick/
│
├── router-on-a-stick.pkt
├── topology.png
├── verification.png
└── README.md
```

---

## 🎯 Project Status

**Status:** Completed / Troubleshooting Documented

**Platform:** Cisco Packet Tracer

**Level:** Beginner → Intermediate

**Main Technology:** Cisco Router-on-a-Stick
