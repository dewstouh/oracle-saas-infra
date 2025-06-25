# 🔒 Advanced Security Guide

Beyond basic hardening, this guide covers advanced security measures, monitoring, and compliance for your Oracle Cloud infrastructure.

## 🛡️ Advanced SSH Hardening

### Multi-Factor Authentication (MFA)

Add MFA to SSH using Google Authenticator:

```bash
# Install Google Authenticator
sudo apt install libpam-google-authenticator -y

# Configure for your user
google-authenticator
```

Follow the prompts:
- **Time-based tokens**: Yes
- **Update ~/.google_authenticator**: Yes
- **Disallow multiple uses**: Yes
- **Increase window**: No (unless you have time sync issues)
- **Enable rate-limiting**: Yes

Configure SSH for MFA:
```bash
sudo nano /etc/pam.d/sshd
```

Add this line at the top:
```
auth required pam_google_authenticator.so
```

Update SSH configuration:
```bash
sudo nano /etc/ssh/sshd_config.d/mfa.conf
```

```bash
# Enable PAM
UsePAM yes

# Require both key and MFA
AuthenticationMethods publickey,keyboard-interactive
ChallengeResponseAuthentication yes
```

Restart SSH:
```bash
sudo systemctl restart ssh
```

### SSH Key Rotation

Automate SSH key rotation:

```bash
# Create key rotation script
nano ~/ssh-key-rotate.sh
chmod +x ~/ssh-key-rotate.sh
```

```bash
#!/bin/bash
BACKUP_DIR="$HOME/.ssh/backup"
DATE=$(date +%Y%m%d)

# Create backup
mkdir -p $BACKUP_DIR
cp ~/.ssh/authorized_keys $BACKUP_DIR/authorized_keys.$DATE

# Generate new key
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_new -N ""

# Add new key to authorized_keys
cat ~/.ssh/id_ed25519_new.pub >> ~/.ssh/authorized_keys

# Remove old keys (keep last 2)
tail -n 2 ~/.ssh/authorized_keys > ~/.ssh/authorized_keys.tmp
mv ~/.ssh/authorized_keys.tmp ~/.ssh/authorized_keys

echo "New SSH key generated. Update your clients with:"
cat ~/.ssh/id_ed25519_new
```

## 🔐 SSL/TLS Security

### SSL Certificate Management

Install and configure certbot with advanced options:

```bash
# Install certbot with DNS plugin for Cloudflare
sudo apt install certbot python3-certbot-dns-cloudflare -y

# Create Cloudflare credentials file
sudo nano /etc/letsencrypt/cloudflare.ini
```

```ini
# Cloudflare API credentials
dns_cloudflare_email = your-email@domain.com
dns_cloudflare_api_key = your-global-api-key
```

Secure the credentials file:
```bash
sudo chmod 600 /etc/letsencrypt/cloudflare.ini
```

Generate wildcard certificate:
```bash
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
  -d yourdomain.com \
  -d *.yourdomain.com
```

### SSL Configuration for Nginx

Create secure SSL configuration:
```bash
sudo nano /etc/nginx/ssl.conf
```

```nginx
# Modern SSL configuration
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;

# SSL session settings
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
ssl_session_tickets off;

# OCSP stapling
ssl_stapling on;
ssl_stapling_verify on;
resolver 1.1.1.1 1.0.0.1 valid=300s;
resolver_timeout 5s;

# Security headers
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
add_header Referrer-Policy "strict-origin-when-cross-origin";
```

## 🔍 Security Monitoring

### Install Security Monitoring Tools

```bash
# Install security monitoring tools
sudo apt install auditd aide rkhunter chkrootkit -y

# Install OSSEC (Host-based IDS)
wget -q -O - https://updates.atomicorp.com/installers/atomic | sudo bash
sudo apt install ossec-hids-server -y
```

### Configure Auditd

```bash
# Configure audit rules
sudo nano /etc/audit/rules.d/audit.rules
```

```bash
# Delete all previous rules
-D

# Buffer settings
-b 8192

# Monitor important files
-w /etc/passwd -p wa -k identity
-w /etc/group -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/sudoers -p wa -k identity
-w /etc/ssh/sshd_config -p wa -k sshd_config

# Monitor system calls
-a always,exit -F arch=b64 -S adjtimex -S settimeofday -k time-change
-a always,exit -F arch=b32 -S adjtimex -S settimeofday -S stime -k time-change

# Monitor network connections
-a always,exit -F arch=b64 -S socket -F success=1 -k network

# Enable audit
-e 1
```

Restart auditd:
```bash
sudo systemctl restart auditd
sudo systemctl enable auditd
```

### Log Monitoring with Logwatch

Configure detailed log monitoring:
```bash
sudo nano /etc/logwatch/conf/logwatch.conf
```

```bash
# Output format
Output = html
Format = html

# Detail level (Low, Med, High, 0-10)
Detail = Med

# Email settings
MailTo = your-email@domain.com
MailFrom = logwatch@yourdomain.com

# Services to monitor
Service = All
Service = "-zz-network"
Service = "-zz-sys"
```

### OSSEC Configuration

Configure OSSEC for intrusion detection:
```bash
sudo nano /var/ossec/etc/ossec.conf
```

Key configurations:
- Enable rootcheck
- Configure email alerts
- Set up real-time monitoring
- Configure custom rules

## 🚨 Intrusion Detection

### Install and Configure Fail2Ban

Advanced Fail2Ban configuration:
```bash
sudo nano /etc/fail2ban/jail.local
```

```ini
[DEFAULT]
# Ban settings
bantime = 3600
findtime = 600
maxretry = 3
backend = systemd

# Email notifications
destemail = your-email@domain.com
sendername = Fail2Ban
mta = sendmail
action = %(action_mwl)s

[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
logpath = /var/log/nginx/error.log
maxretry = 3

[nginx-noscript]
enabled = true
filter = nginx-noscript
logpath = /var/log/nginx/access.log
maxretry = 6
```

### Custom Fail2Ban Filters

Create custom filters for your applications:
```bash
sudo nano /etc/fail2ban/filter.d/coolify.conf
```

```ini
[Definition]
failregex = ^<HOST> -.*"(GET|POST).*" (4|5)\d\d
ignoreregex =
```

## 🔐 File Integrity Monitoring

### Configure AIDE

Initialize AIDE database:
```bash
# Configure AIDE
sudo nano /etc/aide/aide.conf
```

```bash
# Database locations
database=file:/var/lib/aide/aide.db
database_out=file:/var/lib/aide/aide.db.new

# What to monitor
/bin Normal
/sbin Normal
/usr/bin Normal
/usr/sbin Normal
/etc Normal
/home Normal
/var/log Log

# Rules
Normal = p+i+n+u+g+s+m+c+md5+sha1+sha256
Log = p+i+n+u+g+S
```

Initialize and run AIDE:
```bash
# Initialize database
sudo aide --init

# Move database
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Run check
sudo aide --check

# Create daily check script
sudo nano /etc/cron.daily/aide-check
```

```bash
#!/bin/bash
/usr/bin/aide --check | mail -s "AIDE Report $(hostname)" your-email@domain.com
```

## 🔒 Application Security

### Docker Security

Secure Docker configuration:
```bash
sudo nano /etc/docker/daemon.json
```

```json
{
  "live-restore": true,
  "userland-proxy": false,
  "no-new-privileges": true,
  "seccomp-profile": "/etc/docker/seccomp.json",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

### Container Security Scanning

Install container security tools:
```bash
# Install Docker Bench Security
git clone https://github.com/docker/docker-bench-security.git
cd docker-bench-security
sudo sh docker-bench-security.sh

# Install Trivy for vulnerability scanning
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

# Scan Docker images
trivy image nginx:latest
```

## 🛡️ Network Security

### DDoS Protection

Configure network-level DDoS protection:
```bash
# Install DDoS Deflate
wget http://www.inetbase.com/scripts/ddos/install.sh
chmod +x install.sh
sudo ./install.sh
```

Configure DDoS Deflate:
```bash
sudo nano /usr/local/ddos/ddos.conf
```

### Port Knocking

Implement advanced port knocking:
```bash
sudo nano /etc/knockd.conf
```

```ini
[options]
    UseSyslog
    LogFile = /var/log/knockd.log

[openSSH]
    sequence    = 2222:udp,3333:tcp,4444:udp
    seq_timeout = 15
    tcpflags    = syn,ack
    command     = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
    
[closeSSH]
    sequence    = 4444:udp,3333:tcp,2222:udp
    seq_timeout = 15
    tcpflags    = syn,ack
    command     = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
```

## 📊 Security Reporting

### Security Dashboard Script

Create a comprehensive security status script:
```bash
nano ~/security-status.sh
chmod +x ~/security-status.sh
```

```bash
#!/bin/bash
echo "=== Security Status Report $(date) ==="
echo

echo "=== System Updates ==="
apt list --upgradable 2>/dev/null | grep -v "WARNING"
echo

echo "=== SSH Login Attempts ==="
echo "Successful logins (last 10):"
grep "Accepted" /var/log/auth.log | tail -10
echo
echo "Failed login attempts (last 10):"
grep "Failed password" /var/log/auth.log | tail -10
echo

echo "=== Fail2Ban Status ==="
fail2ban-client status
echo

echo "=== UFW Status ==="
ufw status numbered
echo

echo "=== Docker Security ==="
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
echo

echo "=== SSL Certificates ==="
certbot certificates
echo

echo "=== Disk Usage ==="
df -h
echo

echo "=== Memory Usage ==="
free -h
echo

echo "=== Last System Reboot ==="
uptime
last reboot | head -5
```

### Automated Security Reports

Set up automated security reports:
```bash
# Add to crontab
crontab -e
```

```bash
# Daily security report at 6 AM
0 6 * * * /home/justdiego/security-status.sh | mail -s "Daily Security Report" your-email@domain.com

# Weekly vulnerability scan
0 0 * * 0 trivy fs /home | mail -s "Weekly Vulnerability Scan" your-email@domain.com
```

## 🔧 Security Automation

### Automatic Security Updates

Configure automatic security updates:
```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

Configure what to update:
```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```bash
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}";
    "${distro_id}:${distro_codename}-security";
    "${distro_id}ESMApps:${distro_codename}-apps-security";
    "${distro_id}ESM:${distro_codename}-infra-security";
};

Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

## ✅ Advanced Security Checklist

- [ ] MFA enabled for SSH
- [ ] SSL certificates with A+ rating
- [ ] File integrity monitoring (AIDE) configured
- [ ] Intrusion detection (OSSEC) active
- [ ] Container security scanning enabled
- [ ] Network-level DDoS protection
- [ ] Automated security updates configured
- [ ] Security monitoring and alerting
- [ ] Regular security assessments scheduled
- [ ] Incident response plan documented

## 🚨 Incident Response

### Security Incident Playbook

1. **Identify**: Monitor logs and alerts
2. **Contain**: Isolate affected systems
3. **Eradicate**: Remove threats
4. **Recover**: Restore services
5. **Learn**: Update security measures

### Emergency Response Commands

```bash
# Immediate lockdown
sudo ufw deny incoming
sudo systemctl stop docker
sudo systemctl stop nginx

# Check for suspicious processes
ps aux | grep -E "(sh|bash)" | grep -v grep
netstat -tulpn | grep LISTEN

# Analyze logs
sudo grep -i "attack\|breach\|hack\|malware" /var/log/syslog
sudo grep -i "fail" /var/log/auth.log

# Create forensic backup
sudo dd if=/dev/sda of=/backup/forensic-$(date +%Y%m%d).img bs=1M
```
---

**🔒 Remember**: Security is a continuous process. Regularly review and update your security measures!
