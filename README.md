<div align="center">

# 🌐 Enterprise Secure Campus Network Design & Architecture
### Multi-Tier High-Availability Campus Infrastructure with Micro-Segmentation, ACLs & Threat Hardening

[![Cisco Packet Tracer](https://img.shields.io/badge/Simulation-Cisco%20Packet%20Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com/courses/packet-tracer)
[![Network Architecture](https://img.shields.io/badge/Architecture-3--Tier%20Hierarchical-blue?style=for-the-badge)](#)
[![Security Hardened](https://img.shields.io/badge/Security-802.1Q%20%7C%20ACL%20%7C%20DAI%20%7C%20PortSec-22C55E?style=for-the-badge&logo=shield)](https://github.com/bini576)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

<p align="center">
  <a href="#-project-overview">Overview</a> •
  <a href="#-internship-context">Internship Context</a> •
  <a href="#-hierarchical-network-topology">Topology</a> •
  <a href="#-security-policies--hardening">Security Policies</a> •
  <a href="#-configuration-highlights">Configuration Highlights</a> •
  <a href="#-tools--simulation-files">Tools & Files</a>
</p>

</div>

---

## 📌 Project Overview

**Enterprise Secure Campus Network Design** is an end-to-end, defense-in-depth network architecture engineered for modern higher education institutions. The project designs, simulates, and validates a robust campus-wide enterprise network that isolates untrusted traffic, ensures high availability, enforces strict administrative access controls, and mitigates layer-2 and layer-3 attack vectors.

Simulated entirely in **Cisco Packet Tracer**, this architecture addresses the real-world operational challenges of multi-department academic institutions: supporting high-density student Wi-Fi, securing faculty administrative databases, isolating computer science labs, and providing redundant routing pathways to the WAN gateway.

---

## 🏛️ Internship Context & Institutional Scope

During my role as an **IT Security Intern at Jinka Polytechnic College — ICT Directorate (July – September 2025)**, I conducted audits of the college's physical and logical network infrastructure. 

### Core Challenges Addressed:
1. **Flat Network Vulnerabilities:** The legacy setup lacked internal segmentation, exposing administrative records to general student traffic.
2. **Broadcast Storms & Rogue DHCP Servers:** Unauthorized student routers caused IP conflicts and service outages.
3. **Bandwidth Abuse:** Unregulated internet access resulted in bandwidth starvation for mission-critical registrar and financial departments.

This repository represents the **validated architectural blueprint and Cisco IOS configuration suite** designed to overcome these deficiencies.

---

## 🗺️ Hierarchical Network Topology

The architecture follows the industry-standard **Cisco Three-Tier Hierarchical Model** to guarantee modular scalability, performance predictability, and fault isolation:

```
                      [ WAN / Internet Gateway ISP ]
                                    │
                                    ▼
                          ┌──────────────────┐
                          │   Edge Firewall  │
                          │   & Border NAT   │
                          └─────────┬────────┘
                                    │
                         === CORE LAYER ===
                  ┌───────────────────────────────┐
                  │   High-Speed Redundant Core   │
                  │   Switches (Multi-Gigabit L3) │
                  └─────────┬───────────┬─────────┘
                            │           │
                     === DISTRIBUTION LAYER ===
               ┌────────────┴───┐   ┌───┴────────────┐
               │ Admin Distro   │   │ Academic Distro│
               │ L3 Aggregation │   │ L3 Aggregation │
               └────────┬───────┘   └────────┬───────┘
                        │                    │
                        === ACCESS LAYER ===
         ┌──────────────┴───────────────┐    ┌──────────────────────────────┐
         ▼                              ▼    ▼                              ▼
  [ Admin VLAN 10 ]            [ Finance VLAN 20 ]  [ Lab VLAN 30 ]  [ Guest Wi-Fi VLAN 50 ]
  (Registrar / Dean)          (Secured Server Zone) (Isolated Sandbox) (Captive Portal NAT)
```

---

## 🛡️ Security Policies & Hardening Matrix

| Security Feature | Implementation Mechanism | Threat Mitigated |
|:---|:---|:---|
| **VLAN Segmentation** | 802.1Q encapsulation with distinct subnets | Prevents lateral movement between students and admin systems. |
| **Inter-VLAN Routing & ACLs** | Layer 3 Switch SVIs with Extended Access Control Lists | Restricts unauthorized inter-department communication; denies student access to Finance. |
| **Switchport Port Security** | Sticky MAC addressing (`switchport port-security violation shutdown`) | Prevents rogue device attachments and CAM table overflow attacks. |
| **DHCP Snooping** | Trusted core ports vs. untrusted access ports | Completely eliminates rogue DHCP server MITM attacks. |
| **Dynamic ARP Inspection (DAI)** | IP-to-MAC binding inspection via DHCP snooping database | Neutralizes ARP spoofing / ARP poisoning on the LAN. |
| **Spanning Tree (STP) Hardening**| Rapid PVST+ with BPDU Guard & Root Guard | Eliminates bridging loops and prevents rogue switch root takeovers. |
| **Secure Management** | SSHv2, AAA, disabled Telnet/HTTP, encrypted VTY lines | Protects administrative credentials against network sniffing. |

---

## ⚙️ Configuration Highlights (Cisco IOS)

### 1. VLAN Definition & Access Port Hardening
```cisco
! Configure Administration Access Port with Port Security
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation shutdown
 switchport port-security mac-address sticky
 spanning-tree portfast
 spanning-tree bpduguard enable
!
```

### 2. DHCP Snooping & DAI Activation
```cisco
! Enable DHCP Snooping globally and per-VLAN
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,50
! Configure Uplink to Distribution Switch as Trusted
interface GigabitEthernet0/1
 ip dhcp snooping trust
!
! Enable Dynamic ARP Inspection (DAI)
ip arp inspection vlan 10,20,30,50
```

### 3. Extended Access Control List (Isolating Finance VLAN)
```cisco
! Block Student Subnet (172.16.30.0/24) from Finance Server (172.16.20.10)
ip access-list extended ACL_PROTECT_FINANCE
 deny   ip 172.16.30.0 0.0.0.255 host 172.16.20.10
 permit ip 172.16.10.0 0.0.0.255 host 172.16.20.10
 permit tcp any host 172.16.20.10 eq 443
 deny   ip any host 172.16.20.10
 permit ip any any
!
```

---

## 🛠️ Tools & Simulation Files

- **Simulation Engine:** Cisco Packet Tracer v8.2+
- **Protocol Analysis:** Wireshark (packet validation across trunk links)
- **Included Artifacts:**
  - `campus_topology.pkt` — Complete interactive Packet Tracer simulation file.
  - `configs/` — Full running configurations for Core, Distribution, and Access switches.
  - `documentation/` — IP Addressing scheme, subnet allocation sheets, and VLAN dictionaries.

---

## 📸 Screenshots & Topology Diagram

<div align="center">

```text
=================================================================================
               CISCO PACKET TRACER SIMULATION TOPOLOGY OVERVIEW                  
=================================================================================
[ISP Router] <---> [Edge Router 2911] <== 10G Trunk ==> [Core 3650 L3 Switch]
                                                              ||
                  +-------------------------------------------+-------------------+
                  ||                                                              ||
       [Dist-Switch-Admin (L3)]                                       [Dist-Switch-Academic (L3)]
         |                  |                                           |                   |
    [VLAN 10: Admin]   [VLAN 20: Finance]                           [VLAN 30: Labs]   [VLAN 50: Wi-Fi]
=================================================================================
```

*(Place exported Packet Tracer topology diagrams in `docs/images/`)*

`![Topology Overview](docs/images/network_topology_diagram.png)`

</div>

---

## 📄 License & Author

Distributed under the **MIT License**.

- **Author:** [Biniyam Ademasu Afework](https://github.com/bini576)
- **Role:** Cybersecurity Analyst | Security Engineer | Network Designer
- **Internship:** IT Intern, Jinka Polytechnic College ICT Directorate
- **Education:** B.Sc. Cyber Security, Bahir Dar University
- **GitHub:** [@bini576](https://github.com/bini576)
- **LinkedIn:** [linkedin.com/in/biniyam-ademasu](https://linkedin.com/in/biniyam-ademasu)
- **Email:** [admasubiniyam96@gmail.com](mailto:admasubiniyam96@gmail.com)
