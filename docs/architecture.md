# Architecture

## Overview

Iceman is a single-host deployment on a repurposed Lenovo B570 running Debian 13. Docker Compose runs the Immich application stack. Phones and browsers reach the server over the home LAN or through Tailscale when away from home.

## Logical components

- **Immich Server:** web application and API; handles authenticated requests and coordinates library operations.
- **PostgreSQL:** stores application metadata and account/library records.
- **Redis:** supports background job queues.
- **Immich machine-learning service:** performs supported local machine-learning tasks such as face-related processing and smart search.
- **Media storage:** originals and generated media assets reside on storage attached to the Iceman host.
- **PostgreSQL backup job:** creates scheduled database backups separately from the live database.

## Request and upload flow

1. A user signs in to the Immich instance using their own account.
2. The mobile app uploads selected media to Immich over the local network or a Tailscale connection.
3. Immich stores media on the host and records metadata in PostgreSQL.
4. Redis-backed jobs coordinate thumbnail generation, video processing and machine-learning tasks.

## Network paths

- At home, clients can reach the server over the local network.
- Away from home, enrolled devices use Tailscale to establish private, encrypted connectivity.
- MagicDNS provides convenient device-name resolution within the tailnet.

## Conceptual diagram

~~~mermaid
flowchart TB
  subgraph Clients[Family devices]
    Phone1[Owner phone]
    Phone2[Family phone]
    Browser[Web browser]
  end

  subgraph Access[Network access]
    LAN[Home LAN]
    Tailnet[Tailscale tailnet]
  end

  subgraph Host[Iceman · Debian 13]
    Immich[Immich Server]
    PostgreSQL[(PostgreSQL)]
    Redis[(Redis)]
    ML[Immich ML service]
    Media[(Photo and video files)]
    DBBackup[(Database backups)]
  end

  Phone1 --> LAN
  Phone2 --> LAN
  Browser --> LAN
  Phone1 -. remote .-> Tailnet
  Phone2 -. remote .-> Tailnet
  Tailnet --> Immich
  LAN --> Immich
  Immich --> PostgreSQL
  Immich --> Redis
  Immich --> ML
  Immich --> Media
  PostgreSQL --> DBBackup
~~~

This is a logical overview, not a copy of the live Compose file. Exact mount paths, environment values, IP addresses and credentials are intentionally not published.
