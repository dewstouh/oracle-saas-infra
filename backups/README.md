# Backups

This folder is for backup scripts, automation, and manual guides.

## What should be backed up?
- `/status/cachet-data/` (Cachet app data)
- `/status/cachet-db/` (MariaDB for Cachet)
- `/monitoring/uptime-kuma-data/` (Uptime Kuma data)

## Backup Example (Linux)
```bash
tar czf infra-backup-$(date +%F).tar.gz status/cachet-data status/cachet-db monitoring/uptime-kuma-data
