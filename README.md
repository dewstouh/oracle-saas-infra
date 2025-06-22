# Just Diego Infra - My personal infrastructure based on Oracle Cloud

Personal guide to spin up, harden, and maintain an Always Free Oracle Cloud instance, with Coolify, Docker, mail server, all in one with and maximum security.  
If you break something, fix it easily thanks to the backups.

I've been using Oracle cloud services and i broke them down many times. I made this guide so you don't make the same mistakes as i did.

---

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

## 3. Firewall (UFW) — Lock it down

*Allow the needed web ports only (you can add more later).*

```bash
sudo apt install ufw -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status numbered

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

-   If you break something or you **broke** something **in the process**, DONT DELETE YOUR INSTANCE, [check this guide instead](./guides/troubleshooting/reinstall-everything.md).
    
----------

# Important guides to follow after installation

- ### [Backup your server](./guides/backup/README.md)
    *A complete guide to backup your server and restore it in case of failure.*

- ### [Hardening your server](./guides/hardening/README.md)
    *A complete guide to harden your server and make it more secure.*



# Useful tools for server management
- ### [VPS Panel](./guides/extras/vps-panel/README.md)
  *A simple and easy-to-use VPS management panel for your Server.*
![](https://imgur.com/XRZ7DP7.png)
- ### [Mail Service](./guides/extras/mail/README.md)
    *A complete guide to set up a mail server with webmail, IMAP, SMTP, and more.*
![](https://imgur.com/BFSeSem.png)
- ### [Status Page](./guides/extras/uptime-status/README.md)
    *A simple status page to monitor your server's health and uptime.*
![](https://imgur.com/DwaRLHK.png)


----------

**Written and tested by Diego Rodríguez. If you break it, you fix it.**