t# Allow ONLY Cloudflare IPs on HTTP/HTTPS

**This is how you ensure only Cloudflare can access your web server,  
while all direct hits to your IP are blocked (except SSH for you).**

---

## 1. Block everything incoming by default

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
````

---

## 2. Allow SSH for yourself

```bash
sudo ufw allow 22/tcp
# (Or restrict SSH to your IP, as shown in ufw-rules.md)
```

---

## 3. Allow only Cloudflare for web

**Copy the official Cloudflare IPs from [https://www.cloudflare.com/ips/](https://www.cloudflare.com/ips/)**

```
173.245.48.0/20
103.21.244.0/22
103.22.200.0/22
103.31.4.0/22
141.101.64.0/18
108.162.192.0/18
190.93.240.0/20
188.114.96.0/20
197.234.240.0/22
198.41.128.0/17
162.158.0.0/15
104.16.0.0/13
104.24.0.0/14
172.64.0.0/13
131.0.72.0/22
```

Example (you MUST add ALL ranges):

```bash
# IPv4 examples
sudo ufw allow from 173.245.48.0/20 to any port 80
sudo ufw allow from 173.245.48.0/20 to any port 443
sudo ufw allow from 103.21.244.0/22 to any port 80
sudo ufw allow from 103.21.244.0/22 to any port 443
# ...repeat for every range in https://www.cloudflare.com/ips-v4

# IPv6 examples
sudo ufw allow from 2400:cb00::/32 to any port 80
sudo ufw allow from 2400:cb00::/32 to any port 443
# ...repeat for every range in https://www.cloudflare.com/ips-v6
```

---

## 4. Deny everything else

No further rules needed if default deny is set.

---

## 5. Enable and reload

```bash
sudo ufw enable
sudo ufw reload
sudo ufw status numbered
```

---

**Result:**

* Only Cloudflare can hit 80/443 (your web),
* SSH remains open for you (optionally, just from your IP).

**Anyone else trying to access your IP directly gets blocked.**

---

**Tip:**
Keep Cloudflare IP list updated. If you add new ranges,
you can automate the update with a script (see `../scripts/cloudflare-ufw.sh`).
