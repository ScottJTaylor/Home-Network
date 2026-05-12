# Network Topology Diagram

The following diagrams are written in [Mermaid](https://mermaid.js.org/) and will render natively in GitHub.

---

## Full Network Topology

```mermaid
graph TD
    ISP[🌐 ISP Modem\nBridge Mode]
    FW[🔒 OPNsense Firewall\nVLAN Routing · NAT · IDS/IPS · WireGuard]
    CORE[🔀 Core Managed Switch\nTP-Link TL-SG3428X · 24-port]
    PP[📋 48-Port Patch Panel]
    POE[⚡ PoE Switch\nTP-Link TL-SG3428XMP · 400W]
    PVE[🖥️ Proxmox VE Server\nRyzen 7 5700X · 32GB RAM]
    AP1[📡 WiFi AP1\nGround Floor · EAP670]
    AP2[📡 WiFi AP2\nUpper Floor · EAP670]

    CAM1[📷 CAM-01 to CAM-12\n4K PoE IP Cameras]

    ISP --> FW
    FW --> CORE
    CORE --> PP
    CORE --> POE
    CORE --> PVE
    CORE --> AP1
    CORE --> AP2
    PP --> Rooms[🏠 8 Rooms\nWired Ethernet]
    POE --> CAM1
```

---

## VLAN Segmentation

```mermaid
graph LR
    FW[OPNsense Firewall]

    FW -->|VLAN 10\n192.168.10.x| LAN[Main LAN\nPCs · Phones · Laptops]
    FW -->|VLAN 20\n192.168.20.x| CCTV[CCTV Cameras\nNo Internet]
    FW -->|VLAN 30\n192.168.30.x| SRV[Servers\nProxmox · Containers]
    FW -->|VLAN 40\n192.168.40.x| IOT[IoT Devices\nRestricted]
    FW -->|VLAN 50\n192.168.50.x| GUEST[Guest WiFi\nInternet Only]

    CCTV -->|RTSP only| FRIGATE[Frigate NVR\nVLAN 30]
    SRV --> LAN
    LAN -.->|View only\nvia Frigate| CCTV
```

---

## Container Service Map

```mermaid
graph TD
    NPM[Nginx Proxy Manager\nReverse Proxy · SSL]

    NPM --> NC[Nextcloud\nFile Storage]
    NPM --> HA[Home Assistant\nAutomation]
    NPM --> VW[Vaultwarden\nPassword Manager]
    NPM --> PRT[Portainer\nContainer Management]
    NPM --> GF[Grafana\nDashboards]
    NPM --> UK[Uptime Kuma\nMonitoring]

    HA --> FRG[Frigate NVR\nAI Object Detection]
    FRG --> MQTT[Mosquitto\nMQTT Broker]
    MQTT --> HA

    PROM[Prometheus\nNode Exporter] --> IDB[InfluxDB]
    IDB --> GF

    DNS[Pi-hole\nDNS · Ad Blocking] --> ALL[All VLANs\nvia OPNsense DHCP]
```

---

## Build Priority Sequence

```mermaid
gantt
    title Home Lab Build Phases
    dateFormat  X
    axisFormat  Phase %s

    section Foundation
    Server + Proxmox        :done, 1, 2
    OPNsense Firewall       :done, 2, 3
    Core Switch + Cabling   :done, 3, 4
    UPS                     :done, 4, 5

    section Core Services
    CCTV + Frigate          :active, 5, 6
    WiFi APs + Controller   :active, 6, 7

    section Security & Resilience
    WireGuard VPN           :7, 8
    Offsite Backup          :8, 9

    section Observability
    Monitoring Stack        :9, 10

    section Advanced
    PiKVM                   :10, 11
    10GbE Backbone          :11, 12
```
