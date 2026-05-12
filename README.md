# 🖥️ Home Server & Network Infrastructure

> ** Full-stack home network design covering virtualisation, CCTV/NVR, structured cabling, firewall security, and WiFi infrastructure **

---

## 📋 Project Summary

A complete home server and network infrastructure build designed for security, scalability, and self-hosted services. The system is built around **Proxmox VE** as the hypervisor, **OPNsense** as the firewall, and **Frigate NVR** for AI-powered CCTV — all connected via structured Cat6A cabling with strict VLAN segmentation.

| Attribute | Detail |
|---|---|
| **Hypervisor** | Proxmox VE (KVM + LXC) |
| **Firewall** | OPNsense (IDS/IPS, WireGuard VPN) |
| **NVR** | Frigate + Google Coral TPU |
| **Camera Capacity** | 8–12 PoE IP cameras (ONVIF) |
| **VLAN Count** | 5 (LAN, CCTV, Servers, IoT, Guest) |
| **Cabling Standard** | Cat6A throughout (10GbE-ready) |
| **Estimated Budget** | ~$3,245–$4,810 |

---

## 🗂️ Documentation Index

| Document | Description |
|---|---|
| [Architecture Overview](docs/01-architecture.md) | Network topology, VLAN design, traffic flow |
| [Server Hardware](docs/02-server-hardware.md) | Component list, specs, and cost breakdown |
| [Virtualisation Stack](docs/03-virtualisation.md) | Proxmox setup and container service stack |
| [CCTV System](docs/04-cctv.md) | Camera spec, PoE planning, storage calculations |
| [Structured Cabling](docs/05-cabling.md) | Cable runs, patch panel layout, labelling |
| [Network Cabinet](docs/06-network-cabinet.md) | Rack layout, switch design, growth strategy |
| [Firewall & Security](docs/07-firewall-security.md) | OPNsense config, VLAN rules, VPN, IDS |
| [WiFi Infrastructure](docs/08-wifi.md) | AP placement, SSID/VLAN mapping, roaming |
| [Budget & Build Priority](docs/09-budget.md) | Phased build order and total cost summary |
| [Quick Reference](docs/10-quick-reference.md) | IP schema, port map, labels, critical reminders |

---

## 🏗️ High-Level Architecture

```
ISP Modem (Bridge Mode)
         │
   OPNsense Firewall
   (VLAN Routing + NAT + IDS/IPS)
         │
  Core Managed Switch (24-port, Rack-mounted)
    │        │        │        │        │
Patch Panel  PoE SW  Proxmox  WiFi AP1  WiFi AP2
(8 Rooms)  (CCTV)   Server  (Floor 1) (Floor 2)
```

### VLAN Segmentation

| VLAN | Subnet | Purpose | Internet |
|------|--------|---------|----------|
| VLAN 10 | 192.168.10.x | Main LAN — PCs, phones, laptops | ✅ Yes |
| VLAN 20 | 192.168.20.x | CCTV Cameras | ❌ Blocked |
| VLAN 30 | 192.168.30.x | Servers & Containers | ⚠️ Restricted |
| VLAN 40 | 192.168.40.x | IoT Devices | ⚠️ Restricted |
| VLAN 50 | 192.168.50.x | Guest WiFi | ✅ Internet only |

---

## 🔑 Key Design Decisions

- **Cameras isolated on VLAN 20** — no internet access, can only communicate with Frigate NVR container
- **ZFS storage backend** — data integrity, snapshots, and easy rollbacks via Proxmox
- **Google Coral USB TPU** — offloads AI inference from CPU at 8–12 camera scale
- **48-port patch panel** — all 12 camera runs and 8 room runs pre-allocated with room to grow
- **WireGuard VPN** — remote access without any open inbound ports
- **3-2-1 backup rule** — local ZFS + offsite Backblaze B2 via Duplicati/Restic

---

## 🛠️ Tech Stack

`Proxmox VE` `OPNsense` `Frigate NVR` `Home Assistant` `Pi-hole` `WireGuard`
`Nginx Proxy Manager` `Grafana` `InfluxDB` `Prometheus` `Vaultwarden` `Nextcloud`
`Docker` `ZFS` `Cat6A` `802.1Q VLANs` `PoE 802.3at` `ONVIF`

---

## 📁 Repository Structure

```
home-server-portfolio/
├── README.md
├── docs/
│   ├── 01-architecture.md
│   ├── 02-server-hardware.md
│   ├── 03-virtualisation.md
│   ├── 04-cctv.md
│   ├── 05-cabling.md
│   ├── 06-network-cabinet.md
│   ├── 07-firewall-security.md
│   ├── 08-wifi.md
│   ├── 09-budget.md
│   └── 10-quick-reference.md
├── diagrams/
│   └── network-topology.md
└── configs/
    └── vlan-reference.md
```

---
