# Security and privacy

## Data ownership and trust boundaries

Immich is self-hosted software running on the Iceman machine. The photo/video library and Immich database are stored on that host. This is different from uploading the library to an Immich-operated cloud storage account.

Self-hosting does not automatically make a system secure. The operator is responsible for host security, application updates, authentication, network access and backups.

## Tailscale

Tailscale provides encrypted connectivity between authenticated devices. The coordination service helps devices authenticate and establish connections and may process device identity and connection metadata. It should be treated as a trusted third-party component.

## Immich

Immich user accounts and application data are managed by the self-hosted instance. Machine-learning tasks are run by the deployed Immich machine-learning service. Keep the deployment updated using the official upgrade instructions.

## Practical safeguards

- Use unique, strong passwords for Immich accounts and the server's Linux account.
- Keep Debian, Docker and Immich updated.
- Use UFW and avoid exposing Immich directly to the public internet.
- Keep Tailscale membership limited to trusted devices and people.
- Create narrowly scoped API keys for temporary integrations and revoke them when finished.
- Protect database backups as carefully as the live database.
- Do not commit environment files, passwords, API keys, Tailscale state, private media, database files or backup archives to Git.
- Review repository changes before every public commit.

## Public repository hygiene

This repository is documentation only. It intentionally excludes:
- `.env` files and secret values
- API keys, passwords, private keys and certificates
- Tailscale authentication keys and state
- Photos, videos, thumbnails and upload directories
- PostgreSQL data directories and database dumps
- Private IP addresses and personal/family information

The root `.gitignore` is a safety net, not a guarantee. Inspect staged files before committing:

~~~bash
git status --short
git diff --cached --stat
git diff --cached
~~~

If a secret is ever committed, deleting it in a later commit is not enough. Revoke or rotate the exposed credential immediately and then clean repository history as appropriate.
