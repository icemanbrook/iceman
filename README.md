# Iceman — Self-Hosted Family Photo Server

A private, self-hosted photo and video library built on a repurposed laptop. Iceman runs **Immich** on **Debian 13** with Docker Compose and uses **Tailscale** for secure remote access.

The goal: keep the family's photo library under our control instead of depending entirely on a hosted photo-storage service.

> **Project type:** Home lab / self-hosted infrastructure  
> **Status:** Running; family onboarding and backup hardening are ongoing.

## Highlights

- Self-hosted Immich instance running on Debian 13.
- Docker-based deployment with Immich Server, PostgreSQL, Redis and the Immich machine-learning service.
- Remote access through Tailscale and MagicDNS.
- Separate Immich accounts and libraries for family members.
- Google Photos Takeout migration using **immich-go**.
- Automated PostgreSQL database backups.
- Basic host monitoring and maintenance with UFW, unattended-upgrades, SMART monitoring, btop and a custom **iceman-status** command.
- Recovery tested after an unexpected power interruption: the host rebooted, Docker services returned, and the Immich database remained available.

## Hardware

| Component | Specification |
|---|---|
| Host | Lenovo B570 laptop |
| CPU | Intel Core i3-2330M |
| Memory | 4 GB RAM |
| Storage | 320 GB HDD |
| Operating system | Debian 13 |
| Network | Ethernet; Tailscale for remote access |

This is intentionally a low-cost homelab build using repurposed hardware. The modest RAM and HDD are practical constraints, especially during thumbnail generation, video transcoding and machine-learning jobs.

## Software stack

| Layer | Technology | Role |
|---|---|---|
| Operating system | Debian 13 | Host operating system |
| Containers | Docker + Docker Compose | Service deployment |
| Photo management | Immich v3 | Photo/video library and mobile backup |
| Database | PostgreSQL | Immich metadata and application data |
| Cache / queue | Redis | Background job coordination |
| Machine learning | Immich machine-learning container | Local smart search and face-related processing |
| Remote access | Tailscale + MagicDNS | Private connectivity between trusted devices |
| Firewall | UFW | Host firewall |
| Maintenance | unattended-upgrades, SMART monitoring | Security updates and disk-health checks |
| Monitoring | btop, iceman-status | Resource and service visibility |
| Migration | immich-go | Google Photos Takeout import |

## Architecture

~~~mermaid
flowchart TB
    subgraph Clients[Family devices]
        P1[Owner phone]
        P2[Family phone]
        PC[Web browser]
    end

    subgraph Network[Private connectivity]
        LAN[Home LAN]
        TS[Tailscale network]
    end

    subgraph Host[Iceman · Debian 13]
        IMM[Immich server]
        DB[(PostgreSQL)]
        REDIS[(Redis)]
        ML[Machine-learning service]
        MEDIA[(Local photo and video storage)]
        BACKUP[(Database backup directory)]
    end

    P1 --> LAN
    P2 --> LAN
    PC --> LAN
    P1 -. remote access .-> TS
    P2 -. remote access .-> TS
    TS --> IMM
    LAN --> IMM
    IMM --> DB
    IMM --> REDIS
    IMM --> ML
    IMM --> MEDIA
    DB --> BACKUP
~~~

The diagram is conceptual. Actual container names, mount points, ports and Compose settings are defined by the deployment on the host and are intentionally not reproduced here.

## Google Photos migration

Google Photos data was exported with Google Takeout and imported into Immich using **immich-go** on a Windows client.

The Takeout contained **1,583 assets** (1,105 images and 478 videos). The Immich library count was checked against that total, and the Immich job queues were later observed at zero waiting and zero active jobs.

The migration workflow also involved checking original-quality media and allowing Immich to complete background processing such as thumbnails and video transcoding.

## Security and privacy approach

- Photos and videos are stored on the self-hosted machine, not in an Immich-hosted cloud account.
- Tailscale provides encrypted connectivity between enrolled devices. Its coordination service still handles device identity and connection coordination, so it remains a trusted service.
- Immich accounts are managed by this server.
- The server is protected with UFW and kept updated.
- API keys created for one-off migration work should be revoked when no longer needed.
- Secrets, personal media and operational state are deliberately excluded from this public repository.

See [Security and privacy](docs/security.md) for the trust boundaries and practical precautions.

## Backups and recovery

A scheduled PostgreSQL backup script is configured on the host. The photo/video library is stored separately and still needs an independent second copy to provide protection against disk failure or loss of the server.

An unexpected power interruption was used as an unplanned recovery test. The machine booted again, Docker containers recovered, Tailscale reconnected and the database remained intact. This is a useful recovery observation, **not** a substitute for tested off-device backups.

See [Backup and recovery](docs/backup-recovery.md).

## Documentation

- [Architecture](docs/architecture.md)
- [Installation and deployment notes](docs/installation.md)
- [Networking and remote access](docs/networking.md)
- [Security and privacy](docs/security.md)
- [Backup and recovery](docs/backup-recovery.md)
- [Operations and troubleshooting](docs/operations.md)

## Current limitations and next steps

- The host has only 4 GB RAM and a 320 GB HDD; storage capacity and disk performance are the main constraints.
- The laptop battery is not functional, so a loss of charger power causes an immediate shutdown.
- The photo/video library needs a second independent backup destination.
- Family onboarding and validation of each phone's automatic backup are ongoing.
- A full restore drill should be performed for the PostgreSQL backup and media library.

## What I learned

This project provided hands-on experience with Linux server administration, Docker Compose, self-hosted applications, database backups, private networking, firewall configuration, service monitoring, data migration and recovery after an unexpected shutdown.

---

**Note:** This repository documents the project. It does not contain the live Immich configuration, credentials, database, family media or a turnkey installer. Host-specific values are intentionally omitted.
