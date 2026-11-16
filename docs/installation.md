# Installation and deployment notes

This document records the deployment approach and known environment. It is not a turnkey installer: the live Compose file and environment configuration are intentionally not included because they may contain private paths, credentials or deployment-specific settings.

## Host

| Item | Value |
|---|---|
| Hardware | Lenovo B570 laptop |
| CPU | Intel Core i3-2330M |
| RAM | 4 GB |
| Disk | 320 GB HDD |
| OS | Debian 13 |
| Hostname | `iceman` |

## Services

The host runs Docker and Docker Compose with the Immich stack:

- Immich Server
- PostgreSQL
- Redis
- Immich machine-learning service

Additional host tooling includes UFW, unattended-upgrades, SMART monitoring, btop and a custom `iceman-status` command.

## Deployment outline

For a fresh installation, use the current official Immich installation documentation and select the release-appropriate Docker Compose deployment instructions:

1. Install and update Debian.
2. Install Docker Engine and the Compose plugin from their official documentation.
3. Create a dedicated deployment directory with appropriately permissioned media and database storage.
4. Obtain the official Immich Compose file and matching environment example for the chosen release.
5. Review and set strong unique credentials and storage paths in the local environment file.
6. Start the stack with Docker Compose.
7. Create the administrator account through the Immich web interface.
8. Configure firewall access and remote connectivity separately.
9. Configure and test database backups before relying on the server.

Official references:
- [Immich documentation](https://immich.app/docs/overview/introduction)
- [Immich Docker Compose installation](https://immich.app/docs/install/docker-compose)
- [Docker Engine installation](https://docs.docker.com/engine/install/)

## Operational checks

Run these commands on the Debian host:

~~~bash
docker ps
docker compose ps
docker logs --tail 100 immich_server
df -h
free -h
~~~

The actual Compose project directory and service/container names may differ by deployment. Run Compose commands from the directory containing the live Compose file.

## Important hardware constraint

The host has 4 GB RAM and a mechanical HDD. Large imports can keep CPU and disk busy while Immich generates thumbnails, transcodes videos and runs machine-learning jobs. Schedule large imports when the server can remain powered and avoid treating a temporarily quiet log as proof that every job has completed.
