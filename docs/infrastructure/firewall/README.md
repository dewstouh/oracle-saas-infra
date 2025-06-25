# 🔥 Firewall Configuration

Complete firewall setup and configuration for your Oracle Cloud infrastructure, including UFW rules, Oracle Cloud Security Lists, and Cloudflare protection.

## 📋 Overview

A multi-layered firewall approach protects your infrastructure:

```
Internet Traffic
    ↓
Cloudflare WAF (Layer 7)
    ↓
Oracle Cloud Security Lists (Cloud Level)
    ↓
Ubuntu UFW Firewall (OS Level)
    ↓
Your Services
```

## 🛡️ Firewall Layers

### 1. Oracle Cloud Security Lists
- **Cloud-level** protection at the VCN (Virtual Cloud Network) level
- **Stateful** firewall rules
- **Network ACLs** for subnets
- **Default deny** with explicit allow rules

### 2. Ubuntu UFW (Uncomplicated Firewall)
- **Host-based** firewall on your server
- **iptables** frontend with simplified management
- **Application profiles** for common services
- **Logging** and monitoring capabilities

### 3. Cloudflare WAF (Web Application Firewall)
- **Application-layer** protection (Layer 7)
- **DDoS protection** and rate limiting
- **Bot management** and challenge pages
- **Geo-blocking** and IP reputation filtering

## 🚀 Quick Setup

### Essential Firewall Rules

For a basic Oracle Cloud setup with Coolify and mail server:

```bash
# UFW Basic Setup
sudo ufw default deny incoming
sudo ufw default allow outgoing

# SSH (custom port recommended)
sudo ufw allow 2222/tcp comment 'SSH custom port'

# Web services
sudo ufw allow 80/tcp comment 'HTTP'
sudo ufw allow 443/tcp comment 'HTTPS'

# Coolify management
sudo ufw allow 8000/tcp comment 'Coolify panel'

# Mail server
sudo ufw allow 25/tcp comment 'SMTP'
sudo ufw allow 587/tcp comment 'SMTP submission'
sudo ufw allow 993/tcp comment 'IMAPS'
sudo ufw allow 995/tcp comment 'POP3S'

# Enable firewall
sudo ufw enable
```

## 📚 Detailed Guides

### 🔧 UFW Configuration
Comprehensive Ubuntu firewall setup and management.

**📄 [UFW Rules & Configuration](./ufw-rules.md)**
- Basic UFW setup and configuration
- Service-specific rules and profiles
- Advanced UFW features and logging
- Rate limiting and connection tracking
- UFW troubleshooting and maintenance

### ☁️ Cloudflare Protection
Advanced web application firewall and DDoS protection.

**📄 [Cloudflare Allow-Only Setup](./cloudflare-allow-only.md)**
- Cloudflare proxy configuration
- WAF rules and rate limiting
- IP allowlisting and geo-blocking
- Bot management and challenges
- SSL/TLS configuration

## 🔧 Common Firewall Configurations

### Web Server Configuration
```bash
# Allow HTTP/HTTPS traffic
sudo ufw allow 'Nginx Full'
# Or manually:
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### Mail Server Configuration
```bash
# Allow mail server ports
sudo ufw allow 25/tcp    # SMTP
sudo ufw allow 587/tcp   # SMTP Submission
sudo ufw allow 993/tcp   # IMAPS
sudo ufw allow 995/tcp   # POP3S
```

### Database Server Configuration
```bash
# PostgreSQL (local access only)
sudo ufw allow from 10.0.0.0/8 to any port 5432

# MySQL (local access only)  
sudo ufw allow from 10.0.0.0/8 to any port 3306

# Redis (local access only)
sudo ufw allow from 127.0.0.1 to any port 6379
```

### Development Environment
```bash
# Node.js development server
sudo ufw allow 3000/tcp comment 'Node.js dev server'

# React/Vue development server
sudo ufw allow 8080/tcp comment 'Frontend dev server'

# Vite development server
sudo ufw allow 5173/tcp comment 'Vite dev server'
```

## 🚨 Security Best Practices

### 1. Principle of Least Privilege
- **Default deny** all incoming connections
- **Explicit allow** only required services
- **Regular review** and cleanup of rules
- **Time-based access** for temporary services

### 2. Port Security
- **Change default ports** for SSH and admin panels
- **Use non-standard ports** to reduce automated attacks
- **Port knocking** for highly sensitive services
- **VPN access** for administrative tasks

### 3. IP Allowlisting
```bash
# Restrict SSH to specific IPs
sudo ufw delete allow 2222/tcp
sudo ufw allow from YOUR_HOME_IP to any port 2222
sudo ufw allow from YOUR_OFFICE_IP to any port 2222

# Restrict admin panels
sudo ufw allow from TRUSTED_IP to any port 8000
```

### 4. Rate Limiting
```bash
# Rate limit SSH connections
sudo ufw limit ssh comment 'Rate limit SSH'

# Rate limit on specific port
sudo ufw limit 2222/tcp comment 'Rate limit custom SSH'
```

## 📊 Monitoring and Logging

### UFW Logging
```bash
# Enable UFW logging
sudo ufw logging on

# Set log level (low, medium, high, full)
sudo ufw logging medium

# Check UFW logs
sudo tail -f /var/log/ufw.log

# Analyze blocked connections
sudo grep "BLOCK" /var/log/ufw.log | tail -20
```

### Log Analysis
```bash
# Most common blocked IPs
sudo grep "BLOCK" /var/log/ufw.log | awk '{print $12}' | sort | uniq -c | sort -nr | head -10

# Most targeted ports
sudo grep "BLOCK" /var/log/ufw.log | awk '{print $18}' | sort | uniq -c | sort -nr | head -10

# Recent activity
sudo grep "$(date +%b\ %d)" /var/log/ufw.log | tail -20
```

### Automated Monitoring
```bash
# Create firewall monitoring script
nano ~/firewall-monitor.sh
chmod +x ~/firewall-monitor.sh
```

```bash
#!/bin/bash
echo "=== Firewall Status Report $(date) ==="
echo

echo "📊 UFW Status:"
sudo ufw status numbered
echo

echo "🚨 Recent Blocked Connections (last 10):"
sudo grep "BLOCK" /var/log/ufw.log | tail -10
echo

echo "📈 Top Blocked IPs:"
sudo grep "BLOCK" /var/log/ufw.log | awk '{print $12}' | sort | uniq -c | sort -nr | head -5
echo

echo "🔍 Oracle Cloud Security Lists:"
echo "Check OCI Console for security list status"
echo

echo "☁️  Cloudflare Protection:"
echo "Check Cloudflare dashboard for WAF activity"
```

## 🔧 Troubleshooting

### Common Issues

#### Can't Access Services
```bash
# Check UFW status
sudo ufw status verbose

# Check specific port
sudo netstat -tulpn | grep :80

# Test connectivity
curl -I http://localhost:80
telnet localhost 80
```

#### SSH Lockout
```bash
# Use Oracle Cloud Console Connection
# Or temporarily disable UFW:
sudo ufw disable

# Add correct rule and re-enable
sudo ufw allow YOUR_IP
sudo ufw enable
```

#### Performance Issues
```bash
# Check UFW performance
sudo ufw --dry-run status

# Optimize rules order
sudo ufw delete RULE_NUMBER
sudo ufw insert 1 allow from TRUSTED_IP

# Check connection tracking
cat /proc/sys/net/netfilter/nf_conntrack_count
cat /proc/sys/net/netfilter/nf_conntrack_max
```

### Emergency Procedures

#### Temporary Access
```bash
# Temporarily allow all traffic (emergency only)
sudo ufw --force reset
sudo ufw default allow incoming
sudo ufw enable

# Remember to restore proper rules afterward!
```

#### Rule Backup and Restore
```bash
# Backup UFW rules
sudo cp /etc/ufw/user.rules /backup/ufw-rules-$(date +%Y%m%d).bak
sudo cp /etc/ufw/user6.rules /backup/ufw6-rules-$(date +%Y%m%d).bak

# Restore UFW rules
sudo cp /backup/ufw-rules-backup.bak /etc/ufw/user.rules
sudo ufw reload
```

## ✅ Firewall Checklist

### Initial Setup
- [ ] UFW installed and basic rules configured
- [ ] Oracle Cloud Security Lists configured
- [ ] SSH port changed from default (22)
- [ ] Default deny policy enabled
- [ ] Essential services allowed

### Security Configuration
- [ ] Non-standard ports for admin services
- [ ] IP allowlisting for sensitive services
- [ ] Rate limiting enabled for SSH
- [ ] Fail2Ban integration configured
- [ ] Cloudflare WAF enabled (if applicable)

### Monitoring
- [ ] UFW logging enabled and configured
- [ ] Log rotation set up
- [ ] Monitoring scripts created
- [ ] Alert notifications configured
- [ ] Regular security reviews scheduled

### Maintenance
- [ ] Rule backup procedures established
- [ ] Emergency access procedures documented
- [ ] Performance monitoring in place
- [ ] Update procedures defined

## 🔄 Maintenance Schedule

### Daily
- [ ] Review firewall logs for suspicious activity
- [ ] Check for blocked legitimate traffic
- [ ] Monitor service accessibility

### Weekly
- [ ] Analyze blocked connection patterns
- [ ] Review and optimize firewall rules
- [ ] Check Oracle Cloud Security Lists
- [ ] Update Cloudflare WAF rules if needed

### Monthly
- [ ] Full firewall rule audit
- [ ] Performance optimization review
- [ ] Update firewall documentation
- [ ] Test emergency access procedures

## 📚 Additional Resources

### Oracle Cloud Documentation
- [Security Lists Best Practices](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm)
- [Network Security Groups](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/networksecuritygroups.htm)

### UFW Documentation
- [UFW Community Guide](https://help.ubuntu.com/community/UFW)
- [UFW Manual Pages](https://manpages.ubuntu.com/manpages/focal/man8/ufw.8.html)

### Cloudflare Documentation
- [WAF Custom Rules](https://developers.cloudflare.com/waf/custom-rules/)
- [Rate Limiting](https://developers.cloudflare.com/waf/rate-limiting-rules/)
---

**🔥 Remember**: A properly configured firewall is your first line of defense. Regularly review and update your rules!
