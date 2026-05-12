# 05 — Structured Cabling

## Cabling Standard — Cat6A Throughout

**Cat6A** is used for all runs in this design:
- Supports **10GbE** at up to 100m (future-proof)
- Handles PoE heat dissipation better than Cat6
- Minimal cost premium over Cat6

> **Rule:** Cable is cheap. Rewiring inside walls is not. Over-provision now.

---

## Home Run Topology

Every room and camera location gets its own **dedicated cable run** back to the central patch panel. No daisy-chaining, no in-wall splitters.

```
Room 1 ──────────────────────────────┐
Room 2 ──────────────────────────────┤
Room 3 ──────────────────────────────┤
...                                  ├──→ 48-port Patch Panel → Core Switch
CAM-01 ──────────────────────────────┤
CAM-02 ──────────────────────────────┤
WiFi AP1 ────────────────────────────┘
```

---

## 48-Port Patch Panel Layout

| Ports | Zone | Cables |
|---|---|---|
| 1–4 | Room 1 — Home Office | 4 cables |
| 5–8 | Room 2 — Lounge | 4 cables |
| 9–10 | Room 3 | 2 cables |
| 11–12 | Room 4 | 2 cables |
| 13–14 | Room 5 | 2 cables |
| 15–16 | Room 6 | 2 cables |
| 17–18 | Room 7 | 2 cables |
| 19–20 | Room 8 | 2 cables |
| 21–32 | CCTV Cameras CAM-01 to CAM-12 | 12 cables |
| 33–34 | WiFi AP1 + AP2 | 2 cables |
| 35–48 | Reserved / future growth | — |

---

## Cable Installation Guidelines

| Rule | Detail |
|---|---|
| Cable type | Cat6A CMR (riser-rated) — Monoprice recommended |
| Extra length | Add 20–25% per run for slack, bends, and mistakes |
| Cabinet slack | Leave 1–2 feet of slack inside the cabinet |
| Wall plate slack | Leave 12 inches behind each wall plate |
| Electrical separation | Keep Ethernet ≥6 inches from electrical cables |
| Crossing electrical | Cross at 90° only — never run parallel |
| Fastening | Use J-hooks or cable staples — never tight zip ties |
| Termination standard | T568B on both ends |
| Testing | Test every run with a cable tester **before** closing walls |

---

## Termination Equipment

| Item | Recommendation | Approx. Cost |
|---|---|---|
| Cable (1000ft box) | Monoprice Cat6A CMR Riser | $120–$150 |
| Patch Panel (48-port) | Tripp Lite or Leviton Cat6A | $80–$120 |
| Keystone Jacks (×20) | Leviton Cat6A | $40–$60 |
| Wall Plates (6-port) | Leviton modular keystone plates | $5–$8 each |
| Punch Down Tool | Klein Tools VDV427-300 | $15–$25 |
| Cable Tester | Klein Tools VDV501-851 | $50–$70 |
| Label Maker | Brother PT-D210 | $25–$35 |

---

## Labelling Convention

Label every cable at **both ends** before installation.

| Format | Example | Meaning |
|---|---|---|
| `[ROOM]-[PORT]` | `LR-01` | Living Room, Port 1 |
| `[ROOM]-[PORT]` | `BR2-03` | Bedroom 2, Port 3 |
| `CAM-[NUMBER]` | `CAM-07` | Camera 7 |
| `SRV-[NUMBER]` | `SRV-01` | Server Port 1 |
| `AP-[NUMBER]` | `AP-01` | Access Point 1 |

---

## Cable Count Summary

| Area | Cables |
|---|---|
| 8 rooms (2–4 per room) | ~22 cables |
| 12 camera runs (×2 per location) | 24 cables |
| WiFi APs | 2 cables |
| Server / firewall / UPS | ~4 cables |
| **Total estimate** | **~52 cable runs** |
