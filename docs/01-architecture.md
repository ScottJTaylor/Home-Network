# 01 — Architecture Overview

## Network Topology

```
ISP Modem (Bridge Mode)
         │
   ┌─────┴──────┐
   │  OPNsense  │  ← Firewall / Router / VPN / IDS
   │  Firewall  │
   └─────┬──────┘
         │
   ┌─────┴──────────────────┐
   │  Core Managed Switch   │  ← 24-port, VLAN-aware, rack-mounted
   │  TP-Link TL-SG3428X    │
   └──┬──────┬────┬────┬────┘
      │      │    │    │
  Patch    PoE  Proxmox  WiFi
  Panel   Switch  Server  APs
 (8 rooms)(CCTV)(VMs/LXC)(x2)
```

---

## VLAN Design

| VLAN | Subnet | Purpose | Internet Access | Inter-VLAN |
|------|--------|---------|----------------|------------|
| VLAN 10 | 192.168.10.x | Main LAN — PCs, phones, laptops | ✅ Full | Can reach VLAN 30 (servers) |
| VLAN 20 | 192.168.20.x | CCTV Cameras | ❌ Blocked | Frigate container only |
| VLAN 30 | 192.168.30.x | Servers & Containers | ⚠️ Restricted | Can reach all VLANs |
| VLAN 40 | 192.168.40.x | IoT Devices | ⚠️ Restricted | None |
| VLAN 50 | 192.168.50.x | Guest WiFi | ✅ Internet only | Fully isolated |

---

## WiFi SSID → VLAN Mapping

| SSID | VLAN | Purpose |
|------|------|---------|
| `HomeNetwork` | VLAN 10 | Trusted personal devices |
| `IoT_Devices` | VLAN 40 | Smart home, TVs, speakers |
| `Guest` | VLAN 50 | Visitors — internet only |

---

## Traffic Flow Rules

### Camera Traffic
```
Camera (VLAN 20) → Frigate NVR Container (VLAN 30) ✅
Camera (VLAN 20) → Internet                         ❌ BLOCKED
Camera (VLAN 20) → Main LAN (VLAN 10)               ❌ BLOCKED
```

### Main LAN Access
```
Main LAN (VLAN 10) → Internet                        ✅
Main LAN (VLAN 10) → Servers (VLAN 30)               ✅ (view/access)
Main LAN (VLAN 10) → CCTV (VLAN 20)                  ✅ View only via Frigate
```

### IoT / Guest
```
IoT (VLAN 40)   → Internet                           ✅ (restricted)
IoT (VLAN 40)   → Any other VLAN                     ❌ BLOCKED
Guest (VLAN 50) → Internet                           ✅
Guest (VLAN 50) → Any internal VLAN                  ❌ BLOCKED
```

---

## Key Design Principles

1. **Security by default** — firewall denies all inter-VLAN traffic unless explicitly permitted
2. **Camera isolation** — VLAN 20 has a hard block on internet and LAN access; cameras cannot exfiltrate data
3. **Servers as a hub** — VLAN 30 can route to all VLANs as required (Frigate, Home Assistant, Pi-hole)
4. **No open inbound ports** — all remote access via WireGuard VPN on OPNsense
5. **DNS-level ad/tracker blocking** — Pi-hole is the upstream DNS for all VLANs via OPNsense DHCP
