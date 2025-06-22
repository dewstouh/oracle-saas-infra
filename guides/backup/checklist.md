# Backup/Restore Checklist

- [ ] Take Oracle boot volume backup (panel > Compute > Boot Volumes > Backups)
- [ ] Copy all `.env` and config files off the server
- [ ] Back up Docker volumes (see `scripts/backup.sh`)
- [ ] Double-check DNS & Cloudflare settings
- [ ] Save latest SSH keys locally

## Restore Steps

- Restore volume in Oracle panel
- Attach volume as boot volume to new instance (if needed)
- Update DNS/Cloudflare to new IP (if changed)
