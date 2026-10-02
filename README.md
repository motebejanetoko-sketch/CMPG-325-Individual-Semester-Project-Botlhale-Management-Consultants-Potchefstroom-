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
