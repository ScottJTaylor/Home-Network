# 02 — Server Hardware

The server is the core of the infrastructure, running Proxmox VE to host all containers and virtual machines.

## Component List

| Component | Recommendation |
|---|---|
| CPU | AMD Ryzen 7 5700X (8-core/16-thread) |
| Motherboard | IOMMU-capable AM4/AM5 board |
| RAM | 32GB DDR4 (64GB recommended for growth) |
| OS Drive | 250GB NVMe SSD
| Container Storage | 1–2TB NVMe or SATA SSD |
| Data / Backup HDD | 4–8TB HDD |
| Surveillance HDD ×2 | 2× 8TB WD Purple or Seagate SkyHawk |
| NIC | 2.5GbE NIC (optional upgrade) |
| PSU | 550–650W 80+ Gold |
| Case | Mid-tower ATX or rack-mount chassis |

---

## Key Hardware Notes

### IOMMU / PCIe Passthrough
Enable IOMMU in BIOS to allow PCIe device passthrough to VMs. Required since passing through a NIC directly to a VM.

### Storage Backend — ZFS
Use **ZFS** as the Proxmox storage backend:
- Protects against silent data corruption (checksumming)
- Enables instant snapshots before updates or changes
- Supports RAID-Z1 across the two surveillance drives (8TB usable, single-drive redundancy)
- Easy rollback if a container or VM update goes wrong

### Surveillance Drives
**WD Purple** and **Seagate SkyHawk** are rated for 24/7 write cycles and high-duty DVR workloads. Standard desktop drives are not designed for constant write streams from 8–12 cameras and will fail prematurely.

### Google Coral USB TPU
Add a **Google Coral USB TPU ** when running 8–12 camera streams through Frigate. Without it, AI object detection saturates the CPU. The TPU handles inference at the edge, keeping CPU usage low across all other containers.

---

## Scaling Considerations

| Scenario | Recommendation |
|---|---|
| More containers | Upgrade RAM to 64GB |
| More cameras (12+) | Coral TPU becomes essential |
| High-res video storage | Add additional HDD bays |
| Network throughput | Add 10GbE SFP+ NIC |

---

## Drive Layout

```
/dev/nvme0n1  →  250GB  →  Proxmox OS
/dev/nvme1n1  →  2TB    →  Container / VM storage (fast SSD)
/dev/sda      →  8TB    →  ZFS RAID-Z1 Pool (Disk 1 — Surveillance)
/dev/sdb      →  8TB    →  ZFS RAID-Z1 Pool (Disk 2 — Surveillance)
/dev/sdc      →  4–8TB  →  General data / backup target
```
