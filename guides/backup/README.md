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


# Backing up a Boot Volume
To back up a boot volume in Oracle Cloud, follow these steps:
1. **Navigate to the Compute section**: Log in to your Oracle Cloud account and go to the Compute section, on the Storage tab.

![](https://imgur.com/Oy0YfVZ.png)

2. **Select Boot Volumes**: In the left sidebar, click on "Boot Volumes."
3. **Choose the Boot Volume**: Find the boot volume you want to back up and click on it to open its details page.

![](https://imgur.com/CTAHqWd.png)

4. **Create a Backup**: Click on the "Create Backup" button. You will be prompted to enter a name and description for the backup.

![](https://imgur.com/BHmmqEb.png)

5. **Confirm the Backup**: Review the details and click "Create" to start the backup process.
   
6. **Wait for Completion**: The backup process may take some time, depending on the size of the boot volume. You can monitor the progress in the "Backups" tab of the boot volume details page.

# Restoring a Boot Volume Backup
To restore a boot volume from a backup in Oracle Cloud, follow these steps:

1. **Navigate to the Boot volume backup**
![](https://imgur.com/KurxHGM.png)
2. **Select the Backup**: In the "Backups" tab, find the backup you want to restore and click on it to open its details page.
![](https://imgur.com/xf3vgrt.png)
3. **Press on Restore BOot Volume**: Click on the "Restore Boot Volume" button. You will be prompted to confirm the restoration.
![](https://imgur.com/MJxU6TZ.png)