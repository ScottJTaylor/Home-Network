# 10 — Quick Reference

## IP / VLAN Schema

| VLAN | Subnet | Purpose |
|---|---|---|
| VLAN 10 | 192.168.10.x | Main LAN |
| VLAN 20 | 192.168.20.x | CCTV Cameras (no internet) |
| VLAN 30 | 192.168.30.x | Servers & Containers |
| VLAN 40 | 192.168.40.x | IoT Devices |
| VLAN 50 | 192.168.50.x | Guest WiFi (internet only) |

---

## Default Port Assignments — 48-Port Patch Panel

| Ports | Assignment |
|---|---|
| 1–4 | Room 1 (Home Office) — 4 cables |
| 5–8 | Room 2 (Lounge) — 4 cables |
| 9–20 | Rooms 3–8 — 2 cables each |
| 21–32 | CCTV Cameras CAM-01 to CAM-12 |
| 33–34 | WiFi AP1 + AP2 |
| 35–48 | Reserved / future growth |

---

## Cable Labelling Format

| Format | Example | Meaning |
|---|---|---|
| `[ROOM]-[PORT]` | `LR-01` | Living Room, Port 1 |
| `[ROOM]-[PORT]` | `BR2-03` | Bedroom 2, Port 3 |
| `CAM-[NUMBER]` | `CAM-07` | Camera 7 |
| `SRV-[NUMBER]` | `SRV-01` | Server Port 1 |
| `AP-[NUMBER]` | `AP-01` | Access Point 1 |

---

## WiFi SSID Reference

| SSID | VLAN | Subnet |
|---|---|---|
| `HomeNetwork` | VLAN 10 | 192.168.10.x |
| `IoT_Devices` | VLAN 40 | 192.168.40.x |
| `Guest` | VLAN 50 | 192.168.50.x |

---

## Critical Reminders

| ⚠️ | Reminder |
|---|---|
| 🔴 | ISP modem **must be in BRIDGE MODE** — OPNsense handles all routing |
| 🔴 | Cameras (VLAN 20) must have **NO internet access** — firewall rule required |
| 🔴 | **Test every cable run** before closing walls |
| 🟡 | Take **Proxmox snapshots** before any major container or VM changes |
| 🟡 | Keep **documentation updated in Bookstack** after every change |
| 🟡 | UPS must be connected via **USB to Proxmox** for graceful shutdown automation |
| 🟢 | Run **all 12 camera cable routes now** even if only 8 cameras installed initially |
| 🟢 | Label cables at **both ends** before pulling through walls |

---

## Backup Strategy — 3-2-1 Rule

```
3 copies of data
  ├── Copy 1: Live data on Proxmox (ZFS pool)
  ├── Copy 2: Local backup drive (attached to server)
  └── Copy 3: Offsite — Backblaze B2 via Duplicati / Restic

2 different media types
  ├── SSD (live + local)
  └── Cloud (Backblaze B2)

1 offsite copy
  └── Backblaze B2 (~$6/TB/month)
```

---

## Monitoring Stack

| Tool | Purpose |
|---|---|
| Uptime Kuma | Service uptime monitoring and alerting |
| Grafana + InfluxDB | Visual dashboards for server, network, CCTV stats |
| Prometheus + Node Exporter | System metrics collection feeding into Grafana |
| Proxmox built-in alerts | Email/Telegram alerts for high CPU, RAM, or disk usage |

---

## Key Service URLs (Internal DNS via Pi-hole)

| Service | Internal URL |
|---|---|
| Proxmox | `https://proxmox.home:8006` |
| OPNsense | `https://firewall.home` |
| Frigate | `https://frigate.home` |
| Home Assistant | `https://homeassistant.home` |
| Portainer | `https://portainer.home` |
| Nextcloud | `https://nextcloud.home` |
| Grafana | `https://grafana.home` |
| Uptime Kuma | `https://status.home` |
| Vaultwarden | `https://vault.home` |
| Bookstack | `https://docs.home` |
