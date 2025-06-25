# Oracle Cloud Initial Installation Guide

In this guide i'll be explaining how to set up your Oracle Cloud instance after the initial installation, such as updating the system, securing SSH, setting up a firewall, and more.

> If you need to recover your instance without deleting it, check out the [recovery guide](recovery.md).

## 1. Update and upgrade the system

```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y
sudo apt clean
```

----------

## 2. Create user, set up SSH keys, and lock down SSH
*Lock ssh down so it can only be connected VIA private keys.*

-   Generate SSH key pair on your local machine:
    
    ```bash
    ssh-keygen -t ed25519 -C "yourname@yourdomain"
    
    ```
    
-   Add your **public** key to `~/.ssh/authorized_keys` on the server.
    
-   Edit `/etc/ssh/sshd_config.d/example.conf`:
    ```
    nano /etc/ssh/sshd_config.d/example.conf
    ```    

    ```
    Port 22
    PermitRootLogin no
    PasswordAuthentication no
    AllowUsers justdiego
    
    ```
    
-   Restart SSH:
    
    ```bash
    sudo systemctl restart ssh
    ```
    

----------

## 3. Firewall (UFW): Lock it down

*Allow the needed web ports only (you can add more later).*

```bash
sudo apt install ufw -y
sudo ufw default deny incoming # Deny all incoming connections by default
sudo ufw default allow outgoing # Allow all outgoing connections by default
sudo ufw allow 22/tcp # For SSH
sudo ufw allow 80/tcp # For HTTP
sudo ufw allow 443/tcp # For HTTP(s)
sudo ufw enable
sudo ufw status numbered # Lists the current rules
```


## 4. Install and enable Fail2ban

*This will add a cooldown to the entity that tries to mass request our machine, avoiding bruteforce methods.*

```bash
sudo apt install fail2ban -y
sudo systemctl enable --now fail2ban
```
----------


## 5. Backups and Snapshots

-   Make sure to create a backup of the current volume [here](https://cloud.oracle.com/compute/instances) 
    
-   Store `.env`, SSH keys, and important configs outside the server.

![](https://imgur.com/jSEO16t.png)
    

----------

## 6. Restore

-   If you break something or you **broke** something **in the process**, DONT DELETE YOUR INSTANCE, [check this guide instead](./guides/initial-setup/README.md).
    
----------

# Suggested guides to follow after installation
- ### [Backup and Restore](../infrastructure/backup-recovery/backup.md)
    *A complete guide to backup and restore your server, including boot volume backups and Docker volumes.*

- ### [Recovery Guide](../infrastructure/backup-recovery/recovery.md)
    *A guide to recover your server without deleting it, useful if you break something during the setup process.*

- ### [Hardening your server](security-hardening.md)
    *A complete guide to harden your server and make it more secure.*
