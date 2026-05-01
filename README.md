# IT Company VLAN Network Segmentation Architecture


## Project Overview

This project designs, models, and simulates the complete network infrastructure of a mid-sized IT services company. The goal was to migrate from a flat, insecure legacy topology to a **hierarchical, VLAN-segmented architecture** that enforces isolation between departments, optimizes traffic management, and lays the groundwork for intelligent network supervision.

---

## Company Context

The simulated company is a large IT services firm with approximately **1,500 employees**, organized across **3 floors + ground floor**, structured into 6 departments:

| Department | Staff | Data Sensitivity |
|---|---|---|
| Direction & Administration | 300 | High |
| Development (DEV) | 500 | Medium |
| Infrastructure (INFRA) | 200 | Very High |
| Marketing (MKT) | 350 | Low to Medium |
| Servers (SERVERS) | 30 | Critical |
| Guests (GUEST) | 150 | None |

---

## VLAN Architecture

The network is logically segmented into **7 VLANs**, each mapped to a department or function:

| VLAN ID | Name | Subnet | Gateway | Floor |
|---|---|---|---|---|
| 10 | Marketing (MKT) | 172.16.10.0/24 | 172.16.10.1 | Floor 2 |
| 20 | Guest (GUEST) | 172.16.20.0/24 | 172.16.20.1 | Floor 2 |
| 30 | Infrastructure (INFRA) | 172.16.30.0/24 | 172.16.30.1 | Floor 1 |
| 40 | Development (DEV) | 172.16.40.0/24 | 172.16.40.1 | Floor 1 |
| 50 | Direction & Admin | 172.16.50.0/24 | 172.16.50.1 | Floor 3 |
| 60 | Servers | 172.16.60.0/24 | 172.16.60.1 | Ground Floor |
| 99 | Management | — | — | All (admin only) |

> The **Guest VLAN (20)** is fully isolated - internet access only, no internal resources reachable.  
> The **Servers VLAN (60)** uses static IP assignment for all critical services.

---

## Network Hardware

| Device | Model | Qty | Role |
|---|---|---|---|
| Edge Router | Cisco 2811 | 2 | WAN routing, basic firewall, VPN |
| Core Router | Cisco 2911 | 2 | Inter-VLAN routing, HSRP, high performance |
| Multilayer Switch | Cisco 3560-24PS | 2 | Distribution layer, PoE, L3 routing, redundancy |
| Access Switch | Cisco 2960 | 4 | Per-floor access layer, VLAN segmentation |
| Access Point | Access Point-PT | 5 | WiFi 802.11n coverage per department |
| Server | Server-PT | 6 | DHCP, DNS, HTTP, SMTP, FTP services |

---

## Physical Topology

The architecture follows a **3-tier hierarchical model**:

![Network Topology](screenshots/topology.png)

- **Redundant uplinks** — Each access switch connects to both MLS1 and MLS2 (crossover cables)
- **Dual ISP** — R1 and R2 each connect to two ISPs for WAN redundancy
- **WiFi** — One access point per department, PoE-powered, WPA2-secured
- **Cabling** — Serial DCE (WAN), Straight-Through (switch↔hosts), Cross-Over (switch↔switch)

---

## Key Protocols & Technologies

| Protocol | Role |
|---|---|
| **IEEE 802.1Q** | VLAN tagging on trunk links |
| **HSRP** | Gateway redundancy between MLS1/MLS2 and R1/R2 |
| **DHCP** | Centralized IP assignment per VLAN pool via ip helper-address relay |
| **DNS** | Internal name resolution |
| **HTTP/HTTPS** | Internal web applications |
| **SMTP** | Internal mail server |
| **FTP** | File transfer server with user-based permissions |
| **SSH v2** | Secure remote administration on all switches and routers |
| **STP PortFast** | Fast port activation for end-device ports |

---

## Security Measures

- **VLAN isolation** — Departments cannot communicate unless explicitly permitted by inter-VLAN routing rules (ACLs)
- **Guest VLAN** — Completely isolated from all internal resources; internet access only
- **Port security** — MAC address sticky binding on access ports
- **SSH v2** — All CLI remote access enforced via SSH; Telnet disabled
- **Password encryption** — `service password-encryption` applied on all devices
- **Login banners** — Unauthorized access warnings on all devices
- **DHCP scope limits** — Max 245 hosts per VLAN pool; infrastructure IPs reserved statically

---

## Server Infrastructure (VLAN 60 — Ground Floor)

All servers are centralized at the ground floor with **static IP addresses**:

| Server | Service | Notes |
|---|---|---|
| DHCP Server 1 & 2 | Dynamic IP allocation | Redundant pair |
| DNS Server | Name resolution | Internal and external |
| HTTP Server | Web applications | HTTP and HTTPS enabled |
| SMTP Server | Email | Port 25 / 587 |
| FTP Server | File transfer | User authentication |

---

## Repository Structure

```
├── vlans.pkt                        # Cisco Packet Tracer project file
└── README.md
```

> **To open the simulation:** Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) and open `vlans.pkt`.
