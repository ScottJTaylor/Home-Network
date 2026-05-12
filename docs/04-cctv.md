# 04 — CCTV System

## Overview

The system supports **8–12 PoE IP cameras** using Frigate NVR for recording and AI-powered object detection. All cameras must be **ONVIF-compliant**.

> **Critical planning note:** Run all 12 cable routes and patch panel ports upfront — even if only 8 cameras are installed initially. Cabling is the most expensive and disruptive element to retrofit.

---

## Camera Recommendations

| Camera Model | Resolution | Approx. Cost | Notes |
|---|---|---|---|
| Reolink RLC-810A | 4K | $50–$70 each | Best value — recommended for 12-camera builds |
| Amcrest IP8M-2496EW | 4K | $70–$90 each | Well-supported, good night vision |
| Hikvision DS-2CD2143G2-I | 4MP | $80–$120 each | Industry standard, excellent quality |
| UniFi G4 Pro | 4K | $180–$220 each | Premium — best in full UniFi ecosystem |

**Recommended:** Reolink RLC-810A for cost efficiency across a 12-camera deployment.

---

## PoE Switch Options

### PoE Budget Calculation
```
12 cameras × 15W per camera = 180W minimum
Add 25% headroom            = 225W+ required
```

### Option A — Single Large Switch (Recommended)

| Switch | Ports | PoE Budget | Approx. Cost | Notes |
|---|---|---|---|---|
| TP-Link TL-SG2218P | 16-port managed | 150W | ~$150 | Suitable for up to 8–10 cameras |
| TP-Link TL-SG3428XMP | 24-port managed | 400W | ~$350 | **Recommended** — ample headroom for 12 cameras |
| UniFi USW-Pro-24-PoE | 24-port managed | 400W | ~$500 | Premium — best in UniFi ecosystem |

### Option B — Dual 8-Port PoE Switches
Two 8-port PoE switches uplinked to the core switch. ~$100–$140 cheaper upfront. More flexible placement. Each switch handles 6 cameras comfortably within its 61W budget.

---

## Storage Planning

| Cameras | Resolution | Recording Mode | Retention | Est. Storage |
|---|---|---|---|---|
| 8 cameras | 4K | Motion only | 30 days | ~8–10TB |
| 12 cameras | 4K | Motion only | 30 days | ~12–15TB |
| 12 cameras | 4K | Continuous | 7 days | ~15–20TB |
| 12 cameras | 4K | Continuous | 30 days | ~40–50TB |

### Storage Strategy
- Use **2× WD Purple 8TB** in ZFS RAID-Z1 → **8TB usable** with single-drive redundancy
- Enable **motion-only recording** in Frigate → cuts storage usage 60–80% vs continuous
- Tiered retention: motion clips for 30 days, continuous recordings for 7 days only
- Keep CCTV storage on dedicated surveillance-rated drives, separate from OS and container storage

---

## CPU & Hardware at Scale

| Component | 8 Cameras | 12 Cameras |
|---|---|---|
| CPU (Ryzen 7 5700X) | Adequate | Monitor load — still manageable |
| RAM | 32GB sufficient | 32GB fine; 64GB gives headroom |
| Google Coral USB TPU | Strongly recommended | **Essential** — offloads AI from CPU |
| Container SSD | 1TB fine | 2TB recommended — Frigate temp files grow |

---

## Frigate NVR Integration

Frigate connects to cameras over RTSP on VLAN 20. It performs:
- **H.264/H.265 recording** to surveillance drives
- **AI object detection** (person, car, animal) using the Coral TPU
- **MQTT events** published to Mosquitto → consumed by Home Assistant
- **Clips and snapshots** stored per-detection event

### Frigate → Home Assistant Automation Example
```
Frigate detects: person (Camera 3, confidence >70%)
      ↓
MQTT event published to Mosquitto
      ↓
Home Assistant automation triggers
      ↓
Notification sent via Telegram / mobile app
```

---

## Camera Cabling Notes

| Rule | Detail |
|---|---|
| Double-run every location | 2× Cat6A per camera — one active, one spare |
| Run all routes now | Plan and pull all 12 cable routes even if only 8 cameras installed |
| Labelling | Label CAM-01 through CAM-12 at both camera end and patch panel |
| Patch panel | Allocate 12 dedicated ports (ports 21–32 on 48-port panel) |
| Max run length | 100 metres per camera (PoE limit) — plan routes accordingly |
