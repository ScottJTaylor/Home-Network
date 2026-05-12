# VLAN Reference — OPNsense Configuration Guide

This document describes the VLAN configuration to be applied in OPNsense and the core managed switch.

---

## VLAN Definitions

| VLAN ID | Name | Subnet | Gateway | DHCP Range | DNS |
|---|---|---|---|---|---|
| 10 | LAN | 192.168.10.0/24 | 192.168.10.1 | .100–.254 | 192.168.30.x (Pi-hole) |
| 20 | CCTV | 192.168.20.0/24 | 192.168.20.1 | .100–.150 | None (static preferred) |
| 30 | SERVERS | 192.168.30.0/24 | 192.168.30.1 | .10–.50 (static) | 192.168.30.x (Pi-hole) |
| 40 | IOT | 192.168.40.0/24 | 192.168.40.1 | .100–.254 | 192.168.30.x (Pi-hole) |
| 50 | GUEST | 192.168.50.0/24 | 192.168.50.1 | .100–.254 | 1.1.1.1 (Cloudflare) |

---

## OPNsense Firewall Rules Summary

### VLAN 20 — CCTV
```
# Allow cameras to reach Frigate NVR only
pass  in  quick  on  vlan20  proto tcp  from  192.168.20.0/24  to  <frigate_ip>  port  {554, 8554, 8080}
# Block everything else
block in  quick  on  vlan20  all
```

### VLAN 40 — IoT
```
# Allow IoT internet access
pass  in  quick  on  vlan40  proto {tcp, udp}  from  192.168.40.0/24  to  any
# Block IoT from reaching any internal VLAN
block in  quick  on  vlan40  from  192.168.40.0/24  to  192.168.0.0/16
```

### VLAN 50 — Guest
```
# Allow internet only
pass  in  quick  on  vlan50  proto {tcp, udp}  from  192.168.50.0/24  to  !192.168.0.0/16
# Block access to all internal subnets
block in  quick  on  vlan50  from  192.168.50.0/24  to  192.168.0.0/16
```

---

## Static IP Assignments (VLAN 30 — Servers)

| Service | Static IP | Port(s) |
|---|---|---|
| Proxmox Web UI | 192.168.30.10 | 8006 |
| Pi-hole | 192.168.30.20 | 53, 80 |
| Nginx Proxy Manager | 192.168.30.21 | 80, 443 |
| Frigate NVR | 192.168.30.22 | 5000, 8554 |
| Home Assistant | 192.168.30.23 | 8123 |
| Vaultwarden | 192.168.30.24 | 80 |
| Nextcloud | 192.168.30.25 | 80 |
| Portainer | 192.168.30.26 | 9000 |
| Grafana | 192.168.30.27 | 3000 |
| Uptime Kuma | 192.168.30.28 | 3001 |
| Mosquitto MQTT | 192.168.30.29 | 1883 |

---

## Switch Trunk / Access Port Configuration

### Core Switch (TP-Link TL-SG3428X)

| Port | Mode | VLANs | Connected Device |
|---|---|---|---|
| 1–8 | Access | VLAN 10 | Patch panel (room ports) |
| 9 | Trunk | VLAN 20 | PoE camera switch uplink |
| 21 | Trunk | 10, 40, 50 | WiFi AP1 |
| 22 | Trunk | 10, 40, 50 | WiFi AP2 |
| 23 | Trunk | 10, 20, 30, 40, 50 | Proxmox server |
| 24 | Trunk | All | OPNsense WAN/LAN |

---

## WireGuard VPN — Quick Config Reference

```ini
[Interface]
Address    = 10.10.0.1/24
ListenPort = 51820
PrivateKey = <server_private_key>

[Peer]
# Mobile / Laptop client
PublicKey  = <client_public_key>
AllowedIPs = 10.10.0.2/32
```

Client config:
```ini
[Interface]
Address    = 10.10.0.2/24
PrivateKey = <client_private_key>
DNS        = 192.168.30.20   # Pi-hole

[Peer]
PublicKey  = <server_public_key>
Endpoint   = <your_ddns_hostname>:51820
AllowedIPs = 192.168.10.0/24, 192.168.20.0/24, 192.168.30.0/24
```
