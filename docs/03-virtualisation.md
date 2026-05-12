# 03 — Virtualisation Stack

## Hypervisor — Proxmox VE

**Proxmox VE** is the hypervisor for this build:

- Free and open source (Debian-based)
- Supports both **LXC containers** (lightweight) and **KVM virtual machines** (full isolation)
- Clean web UI for management
- Native ZFS integration
- Snapshot and backup support built in
- Active community and excellent documentation

---

## Container Service Stack

All services run as LXC containers or Docker containers within a Proxmox LXC, managed via Portainer.

| Container | Purpose | Notes |
|---|---|---|
| **Pi-hole** | Network-wide DNS + ad blocking | Configured as upstream DNS in OPNsense |
| **Nginx Proxy Manager** | Reverse proxy + SSL termination | Routes by hostname; Let's Encrypt SSL |
| **Portainer** | Docker container management GUI | Visual management of all containers |
| **Uptime Kuma** | Service uptime monitoring + alerting | Monitors all services; sends alerts |
| **Vaultwarden** | Self-hosted password manager | Bitwarden-compatible; internal access only |
| **Nextcloud** | File storage and sync | Self-hosted Google Drive alternative |
| **Frigate** | NVR + AI object detection | Integrates with Home Assistant + Coral TPU |
| **Mosquitto (MQTT)** | Message broker | Required for Frigate camera events |
| **Home Assistant** | Home automation platform | Triggers alerts from Frigate detection events |
| **Grafana + InfluxDB** | Metrics dashboards | Visualises server, network, and CCTV stats |
| **Prometheus + Node Exporter** | Metrics collection | Feeds system data into Grafana |
| **Bookstack** | Internal documentation wiki | Self-hosted wiki for network docs |
| **Duplicati / Restic** | Backup agent | Offsite backup to Backblaze B2 |
| **Omada / UniFi Controller** | WiFi AP management | Manages TP-Link EAP or UniFi APs |

---

## Service Architecture

```
                    ┌──────────────────────┐
                    │    Nginx Proxy Mgr   │  ← HTTPS + hostname routing
                    └──────────┬───────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
  ┌─────┴──────┐    ┌──────────┴──────────┐   ┌──────┴──────┐
  │  Nextcloud │    │   Home Assistant    │   │  Vaultwarden│
  └────────────┘    └────────┬────────────┘   └─────────────┘
                             │
              ┌──────────────┴──────────────┐
              │          Frigate NVR        │  ← AI inference via Coral TPU
              └──────────────┬──────────────┘
                             │
                    ┌────────┴────────┐
                    │    Mosquitto    │  ← MQTT event bus
                    └─────────────────┘

Monitoring:  Prometheus → InfluxDB → Grafana
DNS:         Pi-hole (all VLANs via OPNsense DHCP)
Backup:      Duplicati / Restic → Backblaze B2
```

---

## Proxmox Snapshot Strategy

| Trigger | Action |
|---|---|
| Before any container update | Take Proxmox snapshot |
| Before Frigate config changes | Take Proxmox snapshot |
| Weekly (automated) | Proxmox Backup Server or vzdump to backup drive |
| Before OS-level changes | Full VM/CT backup |

> **Rule:** Snapshot before you break it, not after.

---

## Resource Allocation (Baseline — 32GB RAM)

| Container | vCPU | RAM | Storage |
|---|---|---|---|
| Frigate NVR | 4 | 4GB | 50GB+ |
| Home Assistant | 2 | 1GB | 10GB |
| Nextcloud | 2 | 2GB | 20GB + data volume |
| Pi-hole | 1 | 512MB | 5GB |
| Grafana + InfluxDB | 2 | 2GB | 20GB |
| Nginx Proxy Manager | 1 | 512MB | 5GB |
| Vaultwarden | 1 | 256MB | 5GB |
| Portainer | 1 | 512MB | 5GB |
| Remaining (OS + headroom) | — | ~21GB | — |
