# 07 — Firewall & Security

## Firewall Platform

**OPNsense** running on a dedicated mini PC.

The ISP modem **must be set to bridge mode** — OPNsense handles all routing, NAT, DNS, and traffic rules.

| Option | Type | Approx. Cost | Notes |
|---|---|---|---|
| OPNsense on mini PC | Software + hardware | full control, IDS/IPS, VPN |
| UniFi Dream Machine | Appliance | Easy UI — best in UniFi ecosystem |
| Firewalla Gold | Appliance | Simple, plug-and-play home option |

---

## OPNsense Responsibilities

| Feature | Configuration |
|---|---|
| **VLAN enforcement** | Cameras on VLAN 20 cannot reach internet or main LAN |
| **NAT** | Manages public IP across all internal devices |
| **DHCP** | Serves IP addresses per VLAN with Pi-hole as DNS |
| **DNS** | Points all VLANs to Pi-hole container for ad/tracker blocking |
| **VPN** | WireGuard for secure remote access — no open inbound ports |
| **IDS/IPS** | Suricata plugin for intrusion detection and prevention |
| **Traffic monitoring** | Real-time visibility into every device's traffic |

---

## VLAN Firewall Rules

### VLAN 20 — CCTV (Most Restrictive)
```
ALLOW  VLAN 20  →  Frigate NVR IP (VLAN 30)     [RTSP stream]
DENY   VLAN 20  →  WAN (internet)
DENY   VLAN 20  →  VLAN 10 (main LAN)
DENY   VLAN 20  →  VLAN 40 (IoT)
DENY   VLAN 20  →  VLAN 50 (guest)
```

### VLAN 40 — IoT
```
ALLOW  VLAN 40  →  WAN (internet, restricted)
DENY   VLAN 40  →  VLAN 10
DENY   VLAN 40  →  VLAN 20
DENY   VLAN 40  →  VLAN 30
```

### VLAN 50 — Guest
```
ALLOW  VLAN 50  →  WAN (internet only)
DENY   VLAN 50  →  ALL internal VLANs
```

### VLAN 30 — Servers (Most Permissive)
```
ALLOW  VLAN 30  →  WAN (restricted — updates, backups)
ALLOW  VLAN 30  →  VLAN 10 (serve content to main LAN)
ALLOW  VLAN 30  →  VLAN 20 (Frigate pulls RTSP from cameras)
ALLOW  VLAN 30  →  VLAN 40 (Home Assistant controls IoT devices)
```

---

## WireGuard VPN — Remote Access

WireGuard runs natively on OPNsense. Enables secure remote access to the entire home network from anywhere without opening any inbound ports.

```
Remote Device (phone/laptop)
        │
        │  WireGuard tunnel (UDP)
        │
  OPNsense WireGuard endpoint (dynamic DNS or static IP)
        │
  Full access to VLANs 10, 30 (cameras via Frigate, containers)
```

**Advantages over port forwarding:**
- No exposed services on the internet
- Encrypted tunnel for all traffic
- Fast handshake — near-zero connection overhead
- Peer-based access control

---

## IDS/IPS — Suricata

Enable the **Suricata** plugin in OPNsense:
- Inspects traffic on the WAN interface (and internal VLANs)
- Uses community rule sets (ET Open, Snort VRT)
- Blocks known malicious IPs, exploit attempts, and C2 traffic
- Logs all alerts — pipe to Grafana for dashboarding

---

## DNS Security — Pi-hole Integration

1. Pi-hole runs as a container on VLAN 30
2. OPNsense DHCP pushes Pi-hole's IP as the DNS server for **all VLANs**
3. Pi-hole blocks ads, trackers, and malicious domains at DNS level
4. Unbound DNS on OPNsense acts as upstream recursive resolver for Pi-hole
5. Local hostnames (e.g., `frigate.home`, `portainer.home`) resolve via Pi-hole local DNS records

---

## Physical Security

| Control | Detail |
|---|---|
| Cabinet | All network equipment in a lockable wall-mount cabinet |
| Port security | Enable on managed switches — blocks unauthorised devices |
| BIOS password | Set on Proxmox server to prevent boot from external media |
| Full-disk encryption | Enable on server OS drive |
| Credential vault | Vaultwarden (internal only) — store all device credentials |

---

## Out-of-Band Management — PiKVM

A **PiKVM** (built on Raspberry Pi) provides remote keyboard, video, and mouse access to the server even if the OS is completely frozen or mid-boot.
