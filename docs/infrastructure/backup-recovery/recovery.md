# Oracle Cloud - Recovery Guide (VNC, Netboot, Rescue & Recovery)

A complete survival log of fully wiping and reinstalling an Oracle Cloud VM,  
using netboot.xyz, VNC console, boot volume resizing, and handling every gotcha on the way.  

This is extremely useful for those who want to start fresh with a new OS install, or recover from a broken system without losing data.

**All commands, screenshots, and errors included, nothing left out.**

---

### Requirements:
- An Oracle Cloud VM instance (ARM or x86).
- SSH access to the instance.
- Basic knowledge of Linux commands and SSH.
- A VNC client installed on your local machine (e.g., TigerVNC, Remmina, RealVNC).

## **1. BACKUP OR DIE**

- Always create a **boot volume backup** (and a clone) in Oracle Cloud before touching the disk.
- “No backup, no mercy.”  
  Use the OCI panel: 
  *Compute > Boot Volumes > Backups > Create Backup*

![](https://i.imgur.com/xiaWp7v.png)

![](https://i.imgur.com/7r6iHut.png)
---

## **2. RESIZE THE DISK (Optional but recommended)**

- Go to your VM in Oracle panel, stop the instance.
- Resize the Boot Volume (increase size to, e.g., 200GB).
- Start the instance and SSH in.

![](https://i.imgur.com/AEUikU3.png)

- **Check new disk size:**
    ```bash
    lsblk
    df -h
    ```

- **If new space doesn’t appear in `/`:**  
  On Ubuntu/Debian (LVM setup):
    ```bash
    sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
    sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
    df -h   # Now all the new space should show up in /
    ```

---

## **3. DOWNLOAD NETBOOT.XYZ EFI IMAGE**

- **On the instance:**
    ```bash
    cd /boot
    # For ARM
    wget https://boot.netboot.xyz/ipxe/netboot.xyz-arm64.efi
    # For x86
    wget https://boot.netboot.xyz/ipxe/netboot.xyz.efi
    ```

Or you can download it locally and upload it to the instance from here: https://netboot.xyz/downloads

---

## **4. SETUP VNC/CONSOLE ACCESS**
![](https://imgur.com/qHXZpo9.png)

- Go to the Oracle Cloud Console:  
  *Compute > Instances > [your instance] > Console Connections > Create Console Connection*
- Download or copy the SSH private key provided by Oracle.
- Click **Copy VNC command for Linux/Mac**.

- **SSH Tunnel for VNC:**
    ```bash
    ssh -i ./your-console-private.key -o ProxyCommand='ssh -i ./your-console-private.key -W %h:%p -p 443 [instanceconsole_ocid]@instance-console.[region].oraclecloud.com' -N -L localhost:5900:[instance_ocid]:5900 [instance_ocid]
    ```
    _**Troubleshooting:**_
    - If you get `Permission denied (publickey)`:  
      Double-check the private key used and console connection validity.
    - If `Unable to negotiate with UNKNOWN port 65535: no matching host key type found. Their offer: ssh-rsa`  
      Add:  
      ```bash
      -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedAlgorithms=+ssh-rsa
      ```
    - If you get errors with libcrypto try using LEGACY mode with this command:
        ```
        sudo update-crypto-policies --set LEGACY
        ```

- **Start a VNC client:**  
    ```bash
    vncviewer localhost:5900
    # Or use TigerVNC/Remmina/RealVNC
    ```

- **Login prompt appears:**  
    - If it asks for a user/password, log in as root or with your regular user.

---

## **5. NETBOOT BOOT (via UEFI VNC Console)**

- **Reboot the server:**
    ```bash
    sudo reboot
    ```
- In the VNC window, when you see the TianoCore/UEFI boot screen,  
  **press ESC** quickly to enter UEFI menu.
- Go to:  
  `Boot Maintenance Manager > Boot From File > [disk] > EFI > netboot.xyz.efi`

**_If netboot.xyz.efi does NOT show up:_**
- Recheck that the .efi file is in `/boot` and the correct architecture.
- Sometimes needs a second reboot to detect.
![](https://i.imgur.com/95lsSoM.png)
![](https://i.imgur.com/5bPpmmo.png)
![](https://i.imgur.com/xQo2jIS.png)
![](https://i.imgur.com/2YchNKr.png)

---

## **6. INSTALL OS FRESH (Ubuntu/Debian, etc.)**

- In the netboot.xyz menu, pick the distro/version (e.g., Ubuntu 22.04 LTS “Jammy Jellyfish”).
    - Server variant is fine, if not shown, the installer will do a minimal (no desktop) by default.
- Go through the network install as usual.
    - **When partitioning, edit LVM/root logical volume to use 100% of available space**.
    - If you see only 97GB available (and you have a 200GB disk), use all of it during install, or fix later as above (`lvextend` + `resize2fs`).



---

## **7. COMMON ERRORS ENCOUNTERED**

- **VNC SSH tunnel issues:**  
  - Permission denied, key errors: always check the right key file and OCID.
  - Port negotiation: add correct HostKeyAlgorithms if needed.

- **Netboot not appearing in UEFI:**  
  - Wrong architecture EFI file.
  - File not downloaded/copied to /boot.
  - Sometimes requires multiple reboots.

- **Authorized keys not working:**  
  - Copy your public key carefully to `~/.ssh/authorized_keys` (not the private one!).
  - Check permissions:  
    ```bash
    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys
    ```

- **SSH “WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!”**  
  - After reinstall, delete old host key:  
    ```bash
    ssh-keygen -R [ip-address]
    ```

---

## **8. POST-INSTALLATION HARDENING**

> For a more extensive guide on post-installation hardening, check out the [post-installation guide](postinstall.md).
---

- Update all packages:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
- Harden SSH (disable password login, root login, set AllowUsers, custom port, etc).
- Install UFW, only allow 22/80/443.
- Install Docker, fail2ban.
- Put all domains behind Cloudflare.

---

## **9. BACKUP “FULL CLEAN” STATE**

- After all is set up and secured,  
  **take a new Oracle Cloud boot volume backup**:  
  *Compute > Boot Volumes > Backups > Create Backup*

![](https://imgur.com/jSEO16t.png)

---

## **TIPS & TRICKS / TROUBLESHOOTING**

- **If you get stuck in VNC:**  
  The Oracle serial console always works to fix SSH or boot issues.
- **Copy/paste in VNC:**  
  Usually NOT supported; use a pastebin or type carefully.
- **If you mess up, RESTORE your backup and restart.**

---

**Author:** Diego Rodríguez  
**Year:** 2025  
**If you break it, you fix it.**

