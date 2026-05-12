# 🖧 Home Network Diagram

Interactive topology map for a home server and network infrastructure build.  
Built with React + Vite. No external UI dependencies.

## Features

- Full network topology from ISP → OPNsense → Core Switch → all downstream devices
- All 5 VLANs visualised with colour-coded logical trunks
- Clickable nodes — click any device to see specs and configuration notes
- VLAN summary bar with isolation rules at a glance
- Solid lines = physical links · Dashed lines = VLAN trunks

## Network Overview

| VLAN | Subnet | Purpose |
|------|--------|---------|
| VLAN 10 | 192.168.10.x | Main LAN (PCs, phones, laptops) |
| VLAN 20 | 192.168.20.x | CCTV cameras — no internet |
| VLAN 30 | 192.168.30.x | Servers & containers |
| VLAN 40 | 192.168.40.x | IoT devices — restricted |
| VLAN 50 | 192.168.50.x | Guest WiFi — internet only |

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v18 or higher
- npm (comes with Node)

### Install & Run

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/home-lab-network-diagram.git
cd home-lab-network-diagram

# Install dependencies
npm install

# Start dev server
npm run dev
```

Then open [http://localhost:5173](http://localhost:5173) in your browser.

### Build for Production

```bash
npm run build
# Output is in the dist/ folder — deploy to any static host (GitHub Pages, Netlify, etc.)
```

## Project Structure

```
home-lab-network-diagram/
├── index.html
├── vite.config.js
├── package.json
├── .gitignore
└── src/
    ├── main.jsx              # React entry point
    └── NetworkDiagram.jsx    # Main diagram component
```

## Deploying to GitHub Pages

```bash
# Install gh-pages helper
npm install --save-dev gh-pages

# Add to package.json scripts:
# "deploy": "gh-pages -d dist"

npm run build
npm run deploy
```

## Tech Stack

- [React 18](https://react.dev/)
- [Vite 5](https://vitejs.dev/)
- SVG for the diagram rendering
- Inline styles only — no CSS framework required

## Customising

All node positions, colors, labels, and detail text live in the top of `src/NetworkDiagram.jsx`:

- `nodes` — device definitions (label, position, colour, icon)
- `edges` — connections between nodes
- `details` — bullet-point notes shown in the side panel when a node is selected
- `vlanSummary` — the VLAN bar at the bottom

---

*Based on Home Server & Network Infrastructure Design v1.0, February 2026.*
