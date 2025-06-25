
# UFW — Basic Firewall Rules (Default Setup)

These are the base rules for a secure Oracle Cloud server.
**Only allow SSH, HTTP, and HTTPS. Everything else is blocked.**

---

## 1. Set default policies

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 2. Allow only the necessary ports

```bash
sudo ufw allow 22/tcp      # SSH
sudo ufw allow 80/tcp      # HTTP
sudo ufw allow 443/tcp     # HTTPS
```

---

## 3. Enable UFW and check status

```bash
sudo ufw enable
sudo ufw reload
sudo ufw status numbered
```

---

## 4. Example: Allow only your IP on SSH (optional, PRO hardening)

```bash
sudo ufw allow from YOUR_IP to any port 22
# Replace YOUR_IP with your real public IP
sudo ufw delete allow 22/tcp   # Remove global SSH access if restricting to your IP
```

---

## 5. Example: Allow only Cloudflare on HTTP/HTTPS (DDoS protection)

See [`cloudflare-allow-only.md`](./cloudflare-allow-only.md) in this folder.

---

**TIP:**
UFW rules are persistent and apply after reboot.
To reset all rules:

```bash
sudo ufw reset
```