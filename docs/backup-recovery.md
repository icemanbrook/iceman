# Backup and recovery

## Current state

A scheduled PostgreSQL backup script is configured on the host:

~~~text
~/scripts/immich-db-backup.sh
~~~

Backups are stored under:

~~~text
~/backups/immich-db
~~~

These paths describe the current deployment and are not included as files in this repository.

## Important distinction

A PostgreSQL backup protects Immich's database records and metadata. It does **not** back up the original photo and video files.

The media library needs a separate, independent copy on another disk or machine. A second copy should be verified before deleting originals from any other storage provider or device.

## Recommended backup plan

1. Continue scheduled PostgreSQL backups.
2. Add a separate backup of the complete Immich media library to another physical device or trusted backup destination.
3. Keep at least one backup disconnected or otherwise protected from accidental deletion and ransomware.
4. Monitor backup age, size and job exit status.
5. Periodically test restoring both the database and media files using the official Immich restore guidance.

## Recovery observation

The server experienced an accidental power interruption during a large import. After power was restored:

- Debian booted successfully.
- Docker containers restarted and returned to a healthy state.
- Tailscale reconnected.
- The Immich database remained available.
- The import could be resumed by rerunning the migration workflow and checking for already-imported assets.

This was a useful unplanned recovery test. It does not prove that every kind of disk failure or database corruption is recoverable.

## Safe shutdown

Before a planned shutdown, check that uploads and important background jobs are complete. Then use:

~~~bash
sudo poweroff
~~~

Avoid removing power while the system is writing to disk.

## Restore references

Use the official Immich backup and restore documentation for the deployed release:
[Immich backup and restore](https://immich.app/docs/administration/backup-and-restore)
