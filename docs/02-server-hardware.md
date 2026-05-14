# 02 — Server Hardware

The server is the core of the infrastructure, running Proxmox VE to host all containers and virtual machines.

## 🖥️ Component List

| Component |  |
|---|---|
| CPU | AMD Ryzen 7 5700X (8-core/16-thread) |
| Motherboard | IOMMU-capable AM5 board |
| RAM | 64GB |
| OS Drive | 250GB NVMe SSD |
| Container Storage | 2TB SATA SSD |
| Data / Backup HDD | 2x 8TB HDD |
| Surveillance HDD | 2× Seagate SkyHawk 16TB HDD |
| NIC | 10GbE SFP+ NIC |
| PSU | 850W 80+ Gold |
| Case | Full-sized tower |

---

## 📓 Key Hardware Notes

### IOMMU / PCIe Passthrough
Enable IOMMU in BIOS to allow PCIe device passthrough to VMs. Required since passing through a NIC directly to a VM.

### Storage Backend — ZFS
Use **ZFS** as the Proxmox storage backend:
- Protects against silent data corruption (checksumming)
- Enables instant snapshots before updates or changes
- Supports RAID-Z1 across the two surveillance drives (16TB usable, single-drive redundancy)
- Easy rollback if a container or VM update goes wrong

### Surveillance Drives
 **Seagate SkyHawk** is rated for 24/7 write cycles and high-duty DVR workloads. Standard desktop drives are not designed for constant write streams from 8–12 cameras and will fail prematurely.

### Google Coral USB TPU
**Google Coral USB TPU ** when camera streams through Frigate. Without it, AI object detection saturates the CPU. The TPU handles inference at the edge, keeping CPU usage low across all other containers.

---

## 💾 Drive Layout

```
/dev/nvme0n1  →  250GB  →  Proxmox OS
/dev/nvme1n1  →  2TB    →  Container / VM storage (fast SSD)
/dev/sda      →  16TB    →  ZFS RAID-Z1 Pool (Disk 1 — Surveillance)
/dev/sdb      →  16TB    →  ZFS RAID-Z1 Pool (Disk 2 — Surveillance)
/dev/sdc      →  8TB  →  General data / backup target
```
