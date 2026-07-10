# HomeLab
Overview of my personal HomeLab environment, and the work i've done so far and future plans with it aswell.

## Project Overview
Welcome to my homelab repository! This project serves as my personal sandbox for learning systems administration, network engineering, and containerization. The intended use of this system is to create a home media server, aswell as maintaining a home server with high availability, secure remote access, and automated media management.

---

## Architecture & Stack

### Hardware
* **Host Machine:** Personal M-ATX PC
* **CPU:** [Intel i5-14600k] | **RAM:** [32GB DDR4]
* **Storage:** [1TB NVMe SSD for OS & VMs, 24TB HDD for media]

### Core Technologies
* **Hypervisor:** Proxmox VE (Virtual Environment)
* **Containers:** Linux Containers (LXC) Running Debian 13
* **Remote Access & Networking:** Tailscale (Mesh VPN), Subnet Routing
* **Media Stack:** Jellyfin, Sonarr, Radarr, Prowlarr, Tdarr(In-Progress)

---

## Network Configuration

To keep my environment organized and secure, I use a structured IP schema and secure remote access.

* **Local Subnet:** `192.168.1.x`
* **Static IP Range:** `192.168.1.2` - `192.168.1.30` (Reserved for core infrastructure and containers)
* **Remote Access:** I utilize **Tailscale** installed on the Proxmox host. I have configured it as a **Subnet Router** so I can securely access my local container web UIs from anywhere without exposing ports to the public internet.

---

## Deployed Services

Here are the primary services currently running in my lab:

### Media & Entertainment
* **Jellyfin:** Open-source media streaming server.
* **The "Arr" Stack (Sonarr, Radarr, Prowlarr):** Automated media acquisition and indexer management.
* *Configuration Note:* All media containers utilize bind mounts to securely access centralized storage located at `/mnt/storage/` on the host, mapped to `/storage` inside the containers.

### Infrastructure & Admin (In Progress / Planned)
* **Ad-Blocking:** [AdGuard Home] for network-wide DNS ad-blocking.
* **Homarr** In-Progress

---

## Key Challenges & Learning Outcomes

### 1. Proxmox Networking & DNS
* **Challenge:** Encountered issues where LXC containers couldn't resolve external domains.
* **Solution:** Troubleshot and corrected the Proxmox host's DNS configuration and ensured proper gateway routing, restoring full connectivity to the containers.

### 2. Secure Remote Access
* **Challenge:** Needed a way to manage my server securely while away from home without opening firewall ports.
* **Solution:** Implemented Tailscale and configured a Subnet Router. This taught me the fundamentals of mesh VPNs and secure routing.

---

## Future Roadmap
- [ ] Implement Tdarr to optimize my storage on my media server,
- [ ] Implement automated 3-2-1 backup routines for LXC containers.

---
*Note: All sensitive data such as public IP addresses, MAC addresses, and API keys have been redacted or omitted for security purposes.*
