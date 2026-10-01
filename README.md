# My Homelab

This repository documents my personal homelab, which I built to gain hands-on experience with virtualization, Linux administration, networking, containerization, Docker, storage management, self-hosted applications, and server maintenance.

Rather than running every service directly on one operating system, I use **Proxmox VE** as the main hypervisor. Proxmox allows me to separate services into lightweight Linux containers and virtual machines while managing everything from one system.

My goal with this homelab is not only to host services that I use regularly, but also to create an environment where I can experiment with technologies commonly used in IT and systems administration.

---

## Hardware

| Component | Hardware |
|---|---|
| CPU | Intel Core i5-14600K |
| GPU | Intel Arc B580 |
| Memory | 32 GB DDR4 |
| Boot Storage | 1 TB SSD |
| Hypervisor | Proxmox VE |
| External Storage | TerraMaster DAS |
| DAS Drives | 2 × 4 TB HDD + 2 × 8 TB HDD |

The server is built inside an **ITX system**, allowing me to keep the physical footprint relatively small while still having enough CPU resources to run multiple services simultaneously.

The **Intel Core i5-14600K** provides enough cores and processing power to handle several containers, Docker workloads, media transcoding, and virtual machines.

The **Intel Arc B580** provides hardware acceleration for workloads that can take advantage of the GPU. One example is OpenWebui + Ollama Local AI hosting. I have also experimented with GPU passthrough and hardware acceleration for other containerized applications.

The server uses a **1 TB SSD for the operating system and application data**, while the majority of my media is stored separately on a **TerraMaster Direct Attached Storage enclosure**.

I chose to separate my larger media storage from the operating system because media files consume significantly more storage than the services themselves. The DAS also makes it easier to expand my storage capacity without rebuilding the main server.

---

## Why Proxmox?

<img width="2880" height="1316" alt="image" src="https://github.com/user-attachments/assets/94c86a74-923d-4f1b-8e13-14b9da3eb414" />

I chose **Proxmox VE** as the operating system and hypervisor because it allows me to manage multiple isolated environments from a single web interface.

Instead of installing every application directly onto one Linux installation, I can create individual Linux Containers (LXC) or Virtual Machines for different services.

This gives me several advantages.

Services are easier to troubleshoot because problems can usually be isolated to a single container. Applications can also be restarted independently without affecting the rest of the server.

Containers also allow me to assign resources such as CPU cores, memory, storage, and device access based on the requirements of each application.

Proxmox snapshots and backups also make experimenting safer because I can restore a container or VM if a configuration change causes problems.

Most of my services run inside **LXC containers** because containers require fewer resources than full virtual machines while still providing separation between applications.

For workloads where I want stronger isolation or a complete operating system environment, I use a traditional virtual machine instead.

---

# Proxmox Infrastructure

My current Proxmox environment contains the following containers and virtual machines.

| ID | Service | Type | Purpose |
|---:|---|---|---|
| 100 | Jellyfin | LXC | Media streaming server |
| 101 | Prowlarr | LXC | Indexer management |
| 102 | Radarr | LXC | Movie library management |
| 103 | Sonarr | LXC | TV library management |
| 104 | qBittorrent | LXC | Download client |
| 105 | FlareSolverr | LXC | Web challenge compatibility service |
| 106 | NPMplus | LXC | Reverse proxy and HTTPS management |
| 107 | Seerr | LXC | Media request interface |
| 108 | Open WebUI | LXC | Self-hosted AI interface |
| 109 | Wizarr | LXC | Jellyfin user invitation system |
| 110 | Docker | LXC | Docker host for RomM and Webstation |
| 111 | Homarr | LXC | Homelab dashboard |
| 200 | Project Zomboid | Linux VM | Dedicated game server |

![Proxmox Resources](screenshots/proxmox/proxmox-resources.png)

---

# Media Server

A major use of my homelab is hosting and managing my personal media library.

Several individual services work together rather than relying on one large application to perform every task.

## Jellyfin — LXC 100

![Jellyfin](screenshots/services/jellyfin.png)

**Jellyfin** is the primary media server in my homelab.

It organizes my media library and provides a web interface and applications that allow media to be streamed to different devices.

I chose Jellyfin because it is self-hosted and gives me control over my media server and its configuration.

The Intel GPU in the server can also be used for hardware-accelerated video transcoding. This reduces CPU usage when a video must be converted into another format or resolution while it is being streamed.

---

## Prowlarr — LXC 101

![Prowlarr](screenshots/services/prowlarr.png)

Prowlarr acts as a centralized indexer manager for my media automation applications.

Instead of configuring the same indexers independently in several applications, I can manage them from Prowlarr and synchronize the configuration with other services.

I separated Prowlarr into its own container because it allows me to update, restart, and troubleshoot the service independently.

---

## Radarr — LXC 102

![Radarr](screenshots/services/radarr.png)

Radarr manages the movie portion of my media library.

It helps organize movie files and works with the other applications in my media stack.

Keeping Radarr separate from Jellyfin means Jellyfin can focus on serving media while Radarr handles library-management tasks.

---

## Sonarr — LXC 103

![Sonarr](screenshots/services/sonarr.png)

Sonarr performs a similar role to Radarr but is designed around television series.

Separating movies and television management allows each application to specialize in one type of media while still using the same underlying storage.

---

## qBittorrent — LXC 104

![qBittorrent](screenshots/services/qbittorrent.png)

qBittorrent is the download client used by my media management environment.

I placed it in a separate container because download clients interact with external network connections and storage differently from the rest of my applications.

Separating it also allows me to change its networking or security configuration without affecting Jellyfin or the other services.

---

## FlareSolverr — LXC 105

FlareSolverr provides compatibility for certain web requests used by applications within the media stack.

It runs as its own service because it performs a specific supporting role and does not need to be installed directly inside Prowlarr or another application.

---

# Networking and Remote Access

## NPMplus — LXC 106

![NPMplus](screenshots/networking/npmplus.png)

NPMplus acts as the **reverse proxy** for my homelab.

Internally, most services operate using their own IP addresses and ports. A reverse proxy allows me to place selected services behind easier-to-manage hostnames while also handling HTTPS connections.

This means users do not need to remember addresses such as:

```text
192.168.x.x:8096
```

Instead, services can be accessed through cleaner hostnames.

Running the reverse proxy separately also gives me one central location for managing external web access and SSL certificates.

---

# Media Requests

## Seerr — LXC 107

![Seerr](screenshots/services/seerr.png)

Seerr provides a user-friendly request interface for the media server.

Instead of manually managing every request directly through the administration applications, users can search for content using a simpler interface.

The request can then be passed into the appropriate media-management workflow.

This allows the administrative side of the media server to remain separate from the interface used by normal users.

---

# Self-Hosted AI

## Open WebUI — LXC 108

![Open WebUI](screenshots/services/openwebui.png)

Open WebUI is part of my experimentation with locally hosted AI.

I use this container to experiment with running language models locally rather than relying entirely on externally hosted AI services.

This project has also given me experience with GPU access from containers, Docker, Linux device permissions, and hardware acceleration.

Running the AI environment separately prevents experiments with models or GPU configuration from interfering with the rest of my homelab.

---

# User Management

## Wizarr — LXC 109

![Wizarr](screenshots/services/wizarr.png)

Wizarr helps manage invitations and onboarding for users of my Jellyfin server.

Instead of manually explaining the setup process each time I add a user, Wizarr provides a more organized invitation workflow.

I keep it separate from Jellyfin because it is an additional management tool rather than a required component of the media server itself.

---

# Docker Host

## Docker / RomM / Webstation — LXC 110

![Docker](screenshots/docker/docker-containers.png)

Container 110 acts as one of the more experimental parts of my homelab.

Instead of running a single application, this LXC acts as a **Docker host**.

I created a dedicated Docker environment because some projects are designed specifically around Docker and Docker Compose. Keeping Docker inside its own Proxmox container allows me to experiment with Docker stacks without mixing Docker configuration with the rest of my Proxmox services.

The main applications currently running inside this environment are **RomM** and **Webstation**.

### RomM

![RomM](screenshots/docker/romm.png)

RomM is a self-hosted game library manager.

I use it to organize my game collection and provide a web-based interface for browsing it.

Running RomM through Docker also gave me more experience working with Docker Compose, persistent volumes, container networking, environment variables, databases, and application configuration files.

### Webstation

![Webstation](screenshots/docker/webstation.png)

Webstation works alongside RomM to provide browser-based game streaming sessions.

My configuration currently uses multiple Webstation containers so more than one independent streaming session can exist.

Setting this up required working with several concepts including:

- Docker networking
- Reverse proxies
- GPU device passthrough
- Linux device permissions
- Hardware video encoding
- Container-to-container communication
- Docker Compose configuration

This has been one of the more technically challenging parts of my homelab and has given me useful troubleshooting experience with Linux, Docker, networking, and GPU acceleration.

---

# Homelab Dashboard

## Homarr — LXC 111

![Homarr](screenshots/services/homarr.png)

Homarr provides a central dashboard for my homelab.

Because I run many different web applications, remembering every IP address and port would quickly become inconvenient.

Homarr gives me a central location where I can create shortcuts to the services I use regularly and view basic information about my environment.

While it is not required for the other applications to function, it makes managing the environment much more convenient.

---

# Game Server

## Project Zomboid — VM 200

![Project Zomboid Server](screenshots/services/project-zomboid.png)

My Project Zomboid dedicated server runs differently from most of the applications in my homelab.

Instead of using an LXC container, I created a dedicated **Linux virtual machine**.

A VM provides a complete operating system environment and stronger separation from the Proxmox host.

Since game servers can have different dependencies, configuration files, resource requirements, and networking requirements than my normal web applications, I wanted the server to operate independently from the rest of my infrastructure.

This also gives me additional experience administering a traditional Linux server rather than relying entirely on containers.

---

# Storage

![Storage](screenshots/storage/proxmox-storage.png)

My larger files are stored using a **TerraMaster DAS containing two 4 TB hard drives and two 8 TB hard drives**.

The DAS is primarily used for media and other large files.

Separating this storage from the Proxmox operating-system SSD keeps the operating system and application storage relatively small while allowing the media library to use much larger hard drives.

This design also means I can expand or change my bulk storage independently from the main server.

---

# Network Architecture

A simplified view of my homelab looks like this:

```text
                         Internet
                            |
                         Router
                            |
                     Reverse Proxy
                       NPMplus
                            |
        +-------------------+-------------------+
        |                   |                   |
     Jellyfin             Seerr               RomM
        |                                       |
        |                                  Webstation
        |
 +------+------+ 
 |             |
Radarr       Sonarr
 |             |
 +------Prowlarr------+
          |
      qBittorrent


                     Proxmox VE
                         |
       +-----------------+-------------------+
       |                 |                   |
      LXC               LXC                  VM
   Containers      Docker Host        Project Zomboid
                       |
               +-------+-------+
               |               |
              RomM        Webstation
```

This layout allows the physical server to host several independent services while Proxmox handles resource allocation and isolation between them.

---

# Repository Structure

```text
homelab/
│
├── README.md
│
├── screenshots/
│   ├── proxmox/
│   │   ├── proxmox-dashboard.png
│   │   ├── proxmox-resources.png
│   │   └── proxmox-storage.png
│   │
│   ├── networking/
│   │   └── npmplus.png
│   │
│   ├── storage/
│   │   └── storage-layout.png
│   │
│   ├── docker/
│   │   ├── docker-containers.png
│   │   ├── romm.png
│   │   └── webstation.png
│   │
│   └── services/
│       ├── jellyfin.png
│       ├── prowlarr.png
│       ├── radarr.png
│       ├── sonarr.png
│       ├── qbittorrent.png
│       ├── seerr.png
│       ├── openwebui.png
│       ├── wizarr.png
│       ├── homarr.png
│       └── project-zomboid.png
│
├── diagrams/
│   └── network-diagram.png
│
├── proxmox/
│   └── README.md
│
├── docker/
│   ├── README.md
│   └── compose/
│
└── documentation/
    ├── networking.md
    ├── storage.md
    ├── gpu-passthrough.md
    └── troubleshooting.md
```

---

# What I Have Learned

Building this homelab has given me hands-on experience that is difficult to gain entirely through classroom exercises.

Some of the areas I have worked with include Linux system administration, virtualization, containers, Docker, Docker Compose, networking, reverse proxies, storage management, DNS, HTTPS certificates, GPU passthrough, hardware acceleration, server troubleshooting, application deployment, and maintaining multiple interconnected services.

One of the biggest things I have learned is that deploying an application is usually only the beginning. A working service also depends on networking, permissions, storage, security, backups, updates, and communication with other systems.

Troubleshooting these interactions has become one of the most useful parts of maintaining my homelab.

---

# Future Plans

I plan to continue expanding and improving the environment as I learn more about systems administration and infrastructure.

Areas I would like to explore further include improved monitoring, automated backups, infrastructure documentation, VLAN segmentation, network security, container automation, storage redundancy, and additional self-hosted services.

This repository will continue to document those changes as the homelab evolves.
