# ☁️ Oracle Cloud Setup Guide

This guide walks you through creating and configuring your Always Free Oracle Cloud Infrastructure (OCI) instance.

## 📋 Prerequisites

- Oracle Cloud account (free tier available)
- Basic understanding of Linux/Ubuntu
- SSH client on your local machine

## 🚀 Step 1: Create Oracle Cloud Account

1. Visit [Oracle Cloud](https://cloud.oracle.com/)
2. Click **"Start for free"**
3. Complete the registration process
4. Verify your account via email and phone and debit/credit card (required for identity verification, no charges will be made)

> 💡 **Tip**: You'll get $300 in free credits plus Always Free tier resources!

## 🖥️ Step 2: Create Compute Instance

### Launch Instance Wizard

1. Navigate to **Compute** → **Instances**
2. Click **"Create Instance"**
3. Configure the following:

#### Basic Configuration
- **Name**: `justdiego-main` (or your preferred name)
- **Compartment**: `root` (default)

#### Image and Shape
- **Image**: `Canonical Ubuntu 22.04`
- **Shape**: `VM.Standard.A1.1.Flex` (Always Free eligible)
- **Configuration**: 4 OCPU, 24 GB memory

![](https://i.imgur.com/IZDlnFP.png)

#### Networking
- **VCN**: Create new VCN or use existing
- **Subnet**: Public subnet
- **Assign public IP**: ✅ **Yes**

#### SSH Keys
- **Upload SSH key files**: Upload your public key
- Or **Generate SSH key pair**: Download and save the private key

#### Boot Volume
- **Boot volume size**: 200 GB (maximum for Always Free)

## 🔧 Step 3: Configure Network Security

### Security List Rules

Add these **Ingress Rules** to your security list:

| Protocol | Port Range | Source | Description |
|----------|------------|--------|-------------|
| TCP | 22 | 0.0.0.0/0 | SSH access |
| TCP | 80 | 0.0.0.0/0 | HTTP |
| TCP | 443 | 0.0.0.0/0 | HTTPS |
| TCP | 8000 | 0.0.0.0/0 | Coolify panel |

### Firewall Configuration

1. Go to **Networking** → **Virtual Cloud Networks**
2. Select your VCN → **Security Lists**
3. Edit the **Default Security List**
4. Add the ingress rules above

## 🔑 Step 4: First Connection

### Connect via SSH

```bash
# Replace with your actual IP and key path
ssh -i ~/.ssh/your-private-key ubuntu@YOUR_PUBLIC_IP
```

### Initial System Update

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

## 📝 Important Notes

### Always Free Limitations
- **Compute**: 2 VM.Standard.E2.1.Micro instances
- **Storage**: 200 GB total block storage
- **Network**: 10 TB outbound data transfer per month

### Resource Management
- Monitor your usage in the OCI console
- Set up billing alerts
- Always Free resources never expire (as long as account is active)

## 🔐 Security Best Practices

1. **Change default SSH port** (covered in [Security Hardening](./security-hardening.md))
2. **Disable password authentication** (SSH keys only)
3. **Enable firewall** (UFW + OCI Security Lists)
4. **Regular updates** and monitoring

## 🚨 Troubleshooting

### Can't Connect via SSH
- Check security list rules
- Verify SSH key permissions (`chmod 600 ~/.ssh/your-key`)
- Ensure correct username (`ubuntu` for Ubuntu instances)

### Instance Performance Issues
- Monitor CPU and memory usage
- Consider upgrading to paid shapes if needed
- Check for runaway processes

## ➡️ Next Steps

Once your instance is running:

1. 🖥️ [Initial Server Setup](./initial-server-setup.md) - Configure Ubuntu basics
2. 🔒 [Security Hardening](./security-hardening.md) - Lock down your server
3. 🎛️ [Coolify Installation](./coolify-installation.md) - Install management panel

---

**💡 Pro Tip**: Bookmark the OCI console and keep your SSH keys secure. You'll be using both frequently!
