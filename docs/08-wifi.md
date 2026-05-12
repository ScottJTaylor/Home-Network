# 08 — WiFi Infrastructure

## Overview

Two **managed Wireless Access Points (WAPs)** integrated into the structured cabling design. Each AP connects to the core managed switch via a dedicated Cat6A run and is powered by PoE — no separate power supply required at each AP location.

```
Core Managed Switch
       │               │
(Cat6A + PoE)   (Cat6A + PoE)
       │               │
     AP1             AP2
  (Ground Floor)  (Upper Floor)
```

---

## Access Point Recommendations

Use proper managed WAPs — not consumer routers. They support VLANs, centralised management, and seamless roaming.

| Model | Coverage | WiFi Standard | PoE Required | Approx. Cost |
|---|---|---|---|---|
| TP-Link EAP670 | Large room / open plan | WiFi 6 (AX3000) | 802.3at | ~$90 |
| TP-Link EAP653 | Medium room | WiFi 6 (AX3000) | 802.3at | ~$65 |
| UniFi U6 Lite | Medium room | WiFi 6 | 802.3af | ~$100 |
| UniFi U6 Pro | Large / high-density | WiFi 6 (4×4 MU-MIMO) | 802.3at | ~$160 |

**Recommendation:** TP-Link EAP670 ×2 for best value, or UniFi U6 Lite ×2 if building a full UniFi ecosystem.

---

## SSID → VLAN Mapping

| SSID | VLAN | Subnet | Internet | Purpose |
|---|---|---|---|---|
| `HomeNetwork` | VLAN 10 | 192.168.10.x | ✅ Full | Trusted devices — phones, laptops, tablets |
| `IoT_Devices` | VLAN 40 | 192.168.40.x | ⚠️ Restricted | Smart TVs, speakers, printers |
| `Guest` | VLAN 50 | 192.168.50.x | ✅ Internet only | Visitors — no access to internal network |

> VLAN 50 (Guest) is isolated by OPNsense firewall rule — internet access only, no routing to VLANs 10, 20, 30, or 40.

---

## AP Placement Guidelines

| Rule | Detail |
|---|---|
| Mounting position | Ceiling mount preferred — best omnidirectional coverage |
| Placement | One AP per floor, positioned centrally |
| Cable source | Use structured cabling runs — one dedicated wall port per AP |
| Interference | Keep away from microwaves, cordless phones, thick concrete/brick |
| Outdoor coverage | Add weatherproof AP (UniFi U6 Mesh or TP-Link EAP610-Outdoor) on outdoor wall port |

---

## Roaming Configuration

For seamless roaming between both APs:

| Setting | Detail |
|---|---|
| SSID + Password | Identical on both APs — devices roam automatically |
| **802.11r** | Fast BSS Transition — faster handoffs during calls / video streaming |
| **802.11k** | Neighbour reports — clients know which AP to roam to |
| **802.11v** | BSS Transition Management — AP can suggest roaming to clients |

Both Omada (TP-Link) and UniFi controllers support 802.11r/k/v.

---

## Controller Software

| Controller | For | Runs As | Cost |
|---|---|---|---|
| Omada Controller | TP-Link EAP APs | Docker container on Proxmox | Free |
| UniFi Network Controller | UniFi APs | Docker container on Proxmox | Free |

Both controllers run as containers on the Proxmox server — no dedicated hardware required.

---

## PoE Budget for APs

| Device | PoE Draw | Notes |
|---|---|---|
| TP-Link EAP670 | ~13.5W each | 802.3at required |
| UniFi U6 Lite | ~12W each | 802.3af sufficient |
| UniFi U6 Pro | ~21W each | 802.3at required |
| **2× EAP670 (recommended)** | **~27W total** | Well within any PoE switch budget |

> Do **not** mix APs and cameras on the same PoE switch. Dedicate ports on the core switch or a separate small PoE switch for APs.

---

## Updated VLAN Summary (Including WiFi)

| VLAN | Subnet | Purpose | WiFi SSID |
|---|---|---|---|
| VLAN 10 | 192.168.10.x | Main LAN (wired + trusted wireless) | `HomeNetwork` |
| VLAN 20 | 192.168.20.x | CCTV Cameras — no internet | None — wired only |
| VLAN 30 | 192.168.30.x | Servers & Containers | None — wired only |
| VLAN 40 | 192.168.40.x | IoT Devices | `IoT_Devices` |
| VLAN 50 | 192.168.50.x | Guest Network | `Guest` |
