# 06 — Network Cabinet & Switch Design

## Rack Layout — 12U Wall-Mount Cabinet

| U Position | Equipment |
|---|---|
| U1–U2 | 48-port Cat6A Patch Panel |
| U3 | Cable Management / Brush Panel |
| U4 | Core Managed Switch (24-port) |
| U5 | PoE Switch for Cameras (TP-Link TL-SG3428XMP or dual 8-port) |
| U6–U7 | Proxmox Server (rack-mount) or shelf for tower |
| U8 | OPNsense Firewall (mini PC on shelf) |
| U9 | Rack-mount UPS |
| U10 | Power Distribution Unit (PDU) |
| U11–U12 | Reserved |

---

## Core Switch

**TP-Link TL-SG3428X** — Recommended

- 24× 1GbE ports
- 4× SFP+ 10GbE uplinks
- SFP+ ports allow a cheap 10GbE DAC cable link to the server in the future
- VLAN 802.1Q support, LACP, RSTP
- ~$200–$250

---

## Switch Port Allocation

| Ports | Assignment | VLAN |
|---|---|---|
| 1–8 | Patch panel — rooms (via patch cables) | VLAN 10 |
| 9–20 | Patch panel — cameras (via patch cables to PoE switch) | VLAN 20 (trunk) |
| 21–22 | WiFi AP1 + AP2 | Trunk (VLANs 10, 40, 50) |
| 23 | Proxmox server | VLAN 30 (trunk) |
| 24 | OPNsense uplink | Trunk (all VLANs) |
| SFP+1 | PoE camera switch uplink | Trunk |
| SFP+2–4 | Reserved for future 10GbE | — |

---

## Growth Strategy

| Scenario | Solution | Approx. Cost |
|---|---|---|
| More ports in a room | Add unmanaged switch to existing wall port | $20–$40 |
| More patch panel ports | Add second 24-port patch panel to rack | $50–$80 |
| More switch ports | Add managed switch, uplink to core switch | $100–$200 |
| 10GbE to server | DAC cable + 10GbE NIC (SFP+) | $50–$100 |

---

## UPS — Uninterruptible Power Supply

A UPS is **essential** — sudden power loss can corrupt ZFS storage and damage drives mid-write.

**Cover as a minimum:** Server, firewall, core switch, PoE camera switch.

**Connect via USB** so Proxmox can trigger a graceful automatic shutdown on low battery.

| Option | VA | Approx. Cost |
|---|---|---|
| APC Back-UPS Pro 1500VA | 1500VA | ~$150–$175 |
| CyberPower CP1500PFCLCD | 1500VA | ~$175–$200 |

> **Tip:** Size the UPS to cover at least 15–20 minutes of runtime for a graceful shutdown, not just momentary protection.
