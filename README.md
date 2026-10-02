


# CMPG325 Semester Project — Botlhale Management Consultants

**Project ID:** CMPG325-2026-088  
**Student Name:** MOTEBEJANE, TP  
**Student ID:** 43403638  
**Client ID:** CLI-088  
**Client:** Botlhale Management Consultants (Potchefstroom)  

---

## Milestone 1: Network Architecture & VLSM Design

### 1. Network Overview
This network design provides a secure, segmented, and scalable infrastructure for Botlhale Management Consultants using a `/23` base block (`172.30.60.0/23`). Inter-VLAN routing is handled via Router-on-a-Stick, and traffic access control policies restrict unauthorized access to the CR4 application server (`SRV1-CR4-Server`).

---

### 2. VLSM Subnetting Scheme

| Subnet / Function | Network Address | Subnet Mask | CIDR | Usable IP Range | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **VLAN 10: Department A** | 172.30.60.0 | 255.255.255.128 | /25 | 172.30.60.2 - 172.30.60.126 | 172.30.60.1 |
| **VLAN 20: Department B** | 172.30.60.128 | 255.255.255.128 | /25 | 172.30.60.130 - 172.30.60.254 | 172.30.60.129 |
| **VLAN 30: Server Zone (CR4)** | 172.30.61.0 | 255.255.255.192 | /26 | 172.30.61.2 - 172.30.61.62 | 172.30.61.1 |
| **VLAN 99: Management** | 172.30.61.64 | 255.255.255.192 | /26 | 172.30.61.66 - 172.30.61.126 | 172.30.61.65 |
| **Reserved / Expansion** | 172.30.61.128 | 255.255.255.128 | /25 | 172.30.61.129 - 172.30.61.254 | N/A |

---

### 3. Network Topologies

#### Physical Topology
The physical network consists of end devices connected to the central switch `S1-CoreSwitch` and routed through `R1-Gateway`.

![Physical Topology](docs/Physical_Topology.jpg)

#### Logical Topology & Access Control
Logical segmentation via VLANs, Router-on-a-Stick inter-VLAN routing, and ACL traffic restrictions for the CR4 application server.

![Logical Topology](docs/Logical_Topology.jpg)

















## Milestone 2: Network Implementation & Verification Evidence

### 1. Implementation Details
* **Cisco Packet Tracer File:** `CMPG325_Milestone2.pkt`
* **VLAN Configuration:** Active VLANs 10, 20, 30, 99 configured on `S1-CoreSwitch`.
* **Port Security:** Configured on access interfaces `Fa0/1` and `Fa0/11`.
* **ACL Enforcement:** Applied on `R1-Gateway` sub-interfaces to permit Department A while blocking Department B access to `SRV1-CR4-Server`.

### 2. Verification Evidence Screenshots

#### Department A Ping Test (Permitted Access)
![Ping Success Dept A](docs/Ping_Success_DeptA.png)

#### Department B Ping Test (Blocked Access by ACL)
![Ping Denied Dept B](docs/Ping_Denied_DeptB.png)

---

## Repository Structure
```text
├── docs/
│   ├── Topology_Diagram.png
│   ├── Router_Status.png
│   ├── Ping_Success_DeptA.png
│   └── Ping_Denied_DeptB.png
├── CMPG325_Milestone2.pkt
└── README.md





































# CMPG325 Milestone 2 - Network Implementation
**Student ID:** 43403638  
**Project ID:** CMPG325-2026-088  
**Client:** Botlhale Management Consultants  

---

## 1. Project Overview & Client Requirements
This repository documents the network implementation for Botlhale Management Consultants. The design provides VLAN segmentation, inter-VLAN routing, and policy-based access control.

---

## 2. IP Addressing & Topology Plan
* **Base Network Block:** `172.30.60.0/23`
* **Routing Strategy:** Router-on-a-Stick using `802.1Q` encapsulation on `R1-Gateway` connected to `S1-CoreSwitch`.

| Subinterface / Interface | VLAN | Network Subnet | Subnet Mask | Default Gateway | Assigned Scope / Role |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `GigabitEthernet0/0.10` | VLAN 10 | `172.30.60.0/25` | `255.255.255.128` | `172.30.60.1` | Dept A (`PC1-DeptA`) |
| `GigabitEthernet0/0.20` | VLAN 20 | `172.30.60.128/25` | `255.255.255.128` | `172.30.60.129` | Dept B (`PC2-DeptB`) |
| `GigabitEthernet0/0.30` | VLAN 30 | `172.30.61.0/26` | `255.255.255.192` | `172.30.61.1` | Server Farm (`SRV1-CR4-Server`) |
| `GigabitEthernet0/0.99` | VLAN 99 | `172.30.61.64/26` | `255.255.255.192` | `172.30.61.65` | Management Subnet |

---

## 3. Assigned Technical Feature: Standard ACL 10
Standard ACL 10 was deployed outbound on `R1-Gateway` subinterface `Gi0/0.30` to secure the Server Farm.

### ACL Enforcement Rules:
* **Permit:** Dept A (`172.30.60.0/25`) is permitted to communicate with `SRV1-CR4-Server` (`172.30.61.2`).
* **Deny:** Dept B (`172.30.60.128/25`) is explicitly restricted from accessing the Server Farm.

### Device Configuration Snippet
```text
! Subinterface Configuration
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 172.30.61.1 255.255.255.192
 ip access-group 10 out
 exit

! Standard ACL Definition
access-list 10 permit 172.30.60.0 0.0.0.127
access-list 10 deny any
