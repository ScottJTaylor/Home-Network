# 09 — Budget & Build Priority

## Phased Build Order

Build in this order to ensure security and stability at each stage before adding complexity.

| Priority | Item | Why |
|---|---|---|
| 1 | Server + Proxmox | Core of the entire system |
| 2 | OPNsense Firewall | Security before anything goes online |
| 3 | Core Managed Switch + Cabling | Network foundation |
| 4 | UPS | Protects everything you've built |
| 5 | CCTV Cameras + PoE Switch + Frigate | Security cameras |
| 6 | WiFi APs + Controller Container | Wireless access across floors |
| 7 | WireGuard VPN | Secure remote access |
| 8 | Offsite Backup | Protects your data |
| 9 | Monitoring Stack | Visibility into the system |
| 10 | PiKVM | Saves you when things go very wrong |
| 11 | 10GbE Backbone | Future-proofing when needed |

---

## Total Estimated Budget

| Category | Approx. Cost |
|---|---|
| Server Hardware | $725–$1,050 |
| Firewall (OPNsense mini PC) | $150–$200 |
| Core Switch + Camera PoE Switch | $550–$750 |
| Structured Cabling (materials) | $600–$860 |
| Network Cabinet (12U) | $100–$180 |
| CCTV Cameras (8–12× Reolink RLC-810A) | $400–$840 |
| UPS | $150–$200 |
| PiKVM | $130–$170 |
| WiFi Access Points (2× TP-Link EAP670) | $160–$200 |
| Miscellaneous / Spare Parts | $100–$150 |
| Google Coral USB TPU | ~$60 |
| **TOTAL** | **~$3,245–$4,810** |

---

## Cost Reduction Options

- **Repurpose existing hardware** — a used Dell PowerEdge or HP ProLiant runs Proxmox well
- **Second-hand managed switches** — widely available on eBay; TP-Link and Cisco SG series hold up well
- **Phase the CCTV** — start with 6–8 cameras; cabling is pre-run for expansion
- **Skip PiKVM initially** — add once the server is stable and remote management is needed
- **Consumer router as interim firewall** — replace with OPNsense once cabling is done

---

## Ongoing Costs

| Service | Monthly Cost |
|---|---|
| Backblaze B2 (offsite backup ~2TB) | ~$12/month |
| Dynamic DNS (e.g., DuckDNS) | Free |
| Let's Encrypt SSL | Free |
| Pi-hole, Proxmox, OPNsense licences | Free |
| **Total ongoing** | **~$12/month** |
