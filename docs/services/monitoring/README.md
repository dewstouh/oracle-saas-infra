# 📊 Monitoring Setup

## 📊 Uptime Kuma Status Page

#### Monitor your server's health and uptime

<p align="center"><a href="https://uptime.kuma.pet/"><img src="https://img.shields.io/badge/Uptime_Kuma-Status_Monitor-00D4AA?style=for-the-badge&#x26;logo=statuspage&#x26;logoColor=white" alt="Uptime Kuma"> </a><a href="https://coolify.io/"><img src="https://img.shields.io/badge/Coolify-Deployed-6366F1?style=for-the-badge&#x26;logo=coolify&#x26;logoColor=white" alt="Coolify"> </a><a href="https://www.docker.com/"><img src="https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&#x26;logo=docker&#x26;logoColor=white" alt="Docker"></a><br><img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="License"> <img src="https://img.shields.io/badge/Status-Ready-4CAF50?style=for-the-badge&#x26;logo=checkmarx&#x26;logoColor=white" alt="Status"></p>

**Official Website:** [uptime.kuma.pet](https://uptime.kuma.pet/) | **GitHub:** [louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)

***

![](https://i.imgur.com/JNQGBbr.png)

***

### 📋 Overview

This guide will help you set up **Uptime Kuma**, a self-hosted monitoring tool that tracks the uptime and performance of your websites, APIs, and services. Using Coolify for deployment makes the process simple and manageable.

### ✨ Features

* 📈 **Real-time Monitoring** - Track uptime, response times, and SSL certificates
* 🔔 **Multi-channel Notifications** - Discord, Slack, Email, Telegram, and 90+ notification services
* 📊 **Beautiful Status Pages** - Public status pages for your services
* 🎨 **Modern UI** - Clean and responsive web interface
* 🔒 **Secure** - Self-hosted with optional authentication
* 📱 **Mobile Friendly** - Works perfectly on all devices

> 🎯 **Target Environment**: Deployed via Coolify on your self-hosted infrastructure

### 🤔 Why use Uptime Kuma?

<details>

<summary>Click to expand</summary>

Uptime Kuma allows you to monitor all your services and websites **without relying on third-party monitoring services**. It provides a **beautiful and intuitive interface** for **tracking uptime**, **response times**, and **receiving instant notifications** when something goes wrong.

While you could rely on external monitoring services, **self-hosting gives you complete control** over your monitoring data and **eliminates monthly subscription costs**. As your infrastructure grows, Uptime Kuma **scales with you**, allowing you to **monitor unlimited services** from a single dashboard. It also supports **public status pages**, so your users can see service availability in real-time, and the best part is, **it's completely free and open source**.

</details>

### ⚙️ Prerequisites

| Component      | Requirement                                          |
| -------------- | ---------------------------------------------------- |
| 🖥️ **Server** | Coolify panel installed and configured               |
| 🌐 **Domain**  | Subdomain for status page (optional but recommended) |
| 🐳 **Docker**  | Environment ready (handled by Coolify)               |
| 💾 **Storage** | Minimal storage for monitoring data                  |

> ⚠️ **Important**: Uptime Kuma is lightweight and can run on minimal resources.

> 💡 **Tip**: Consider using a subdomain like `status.yourdomain.com` for better organization.

### 🚀 Steps to Set Up Uptime Kuma

#### 1. Create New Project in Coolify

1. **Access your Coolify dashboard**
2. **Navigate to Projects** → **Add Project**
3. **Enter project details:**
   * **Name:** `Uptime Kuma`
   * **Environment:** `Production` ![](https://i.imgur.com/2fqR2Vl.png)
4. **Add a new resource** → **Docker Compose** ![](https://i.imgur.com/q3bdNrI.png)

#### 2. Docker Compose Configuration

Use the following Docker Compose configuration in Coolify:

```yaml
services:
  uptime-kuma:
    image: 'louislam/uptime-kuma:2.0.0-beta.3'
    environment:
      - SERVICE_FQDN_UPTIMEKUMA_3001=status.yourdomain.com
      - UPTIME_DOMAIN=status.yourdomain.com
      - UPTIME_PORT=3001
    volumes:
      - 'uptime-kuma:/app/data'
    healthcheck:
      test:
        - CMD-SHELL
        - extra/healthcheck
      interval: 2s
      timeout: 10s
      retries: 15
```

#### 3. Deployment Steps

1. **Paste the Docker Compose configuration** in Coolify ![](https://i.imgur.com/XWuEwl2.png)
2. **Configure your domain** under the `Services` ![](https://i.imgur.com/hCQEBix.png)
3. **Deploy the service** by clicking the Deploy button
4. **Wait for deployment** to complete (usually 1-2 minutes)
5. **Access your Uptime Kuma instance** at your configured domain ![](https://i.imgur.com/H94I2g4.png)

#### 4. Monitoring a Service

1. **Open your Uptime Kuma URL** (e.g., `https://kuma.yourdomain.com`)
2. **Create your admin account** on first visit
3. **Add your first monitor**:
   * Monitor Type: HTTP(s)
   * Friendly Name: Your service name
   * URL: The URL you want to monitor
   * Heartbeat Interval: 60 seconds (recommended)

![](https://i.imgur.com/HjBiCto.png)

#### 5. Creating a status page

1. **Navigate to Status Pages** in the Uptime Kuma dashboard
2. **Add New Status Page**
3. **Configure:**
   1. Page title and description
   2. Select monitors to display
   3. Custom branding and colors
   4. Public URL slug (e.g., `Infra` for `https://kuma.yourdomain.com/status`) ![](https://i.imgur.com/FSrOk8O.png)
4. **Save and publish** your status page
5. **Set the status page domain** to `status.yourdomain.com` ![](https://i.imgur.com/86LXWCy.png)

#### 6. Accesing the Status Page

Now, if we try to access the status page at `https://status.yourdomain.com`, it won't work because we Uptime Kuma is running under traefik, which is a reverse proxy, so we need to add a new rule to the traefik configuration.

1. **Open your Coolify dashboard**
2. **Navigate to Projects** → **Uptime Kuma**
3. **Click on the general** → **Edit Compose File** ![](https://i.imgur.com/MYWfwvm.png)
4. **Add the following rule** under the `labels` section:

```yaml
      - "traefik.enable=true"
      - "traefik.http.routers.kuma.rule=Host(`status.justdiego.com`)"
      - "traefik.http.routers.kuma.entrypoints=http"
      - "traefik.http.services.kuma.loadbalancer.server.port=3001"
      - "traefik.docker.network=coolify"
```

![](https://i.imgur.com/3OG5fXT.png)

> Replace `status.justdiego.com` with your actual domain.

5. **Save the changes** and redeploy the service

Now it should work properly, if you want to add another domain, you can add another rule like this:

```yaml
      - "traefik.http.routers.kuma.rule=Host(`status2.justdiego.com`)"
```

![](https://i.imgur.com/JNQGBbr.png)

### Services & Monitoring

Now that you have Uptime Kuma set up and running, you can start monitoring all your services and websites. Uptime Kuma makes it easy to track the health of your entire infrastructure from a single dashboard.

If you want to add monitors now, you can explore the Uptime Kuma dashboard and add services directly. Additionally, this monitoring solution works perfectly with the other services in this infrastructure guide:

#### What You Can Monitor:

* [**Coolify Panel**](../uptime-status/) - Monitor your VPS management panel uptime
* [**Mail Server**](../mail/) - Track mail server availability and SMTP/IMAP services
* [**Websites & APIs**](../uptime-status/) - Monitor any HTTP/HTTPS endpoints
* **External Services** - Monitor third-party APIs and services you depend on

These monitoring capabilities will help you maintain high availability and quickly respond to any issues with your self-hosted infrastructure.

### 🔧 Configuration

#### Adding Monitors

**Supported Monitor Types:**

* 🌐 **HTTP(s)** - Websites and APIs
* 🔌 **TCP Port** - Database connections, SSH, etc.
* 🏓 **Ping** - Server connectivity
* 🔒 **DNS** - Domain resolution
* 📧 **SMTP** - Email server status

#### Setting up Notifications

1. **Go to Settings** → **Notifications**
2. **Add Notification** and choose your preferred service:
   * Discord Webhook
   * Slack
   * Email (SMTP)
   * Telegram
   * And 90+ other services

### 🎨 Customization

#### Custom Themes

* Light and dark themes available
* Custom CSS for advanced styling
* Logo and branding customization

#### API Access

* RESTful API for integrations
* Monitor management via API
* Status data export

### 🚨 Troubleshooting

#### Common Issues

**Service not accessible:**

* Check domain configuration in environment variables
* Verify Coolify proxy settings
* Ensure port 3001 is properly mapped

**Monitors not working:**

* Verify network connectivity from container
* Check firewall rules
* Validate monitor URLs and settings

**Performance issues:**

* Monitor the container resources
* Reduce monitoring frequency if needed
* Clean up old monitoring data

***

### 👨‍💻 Author

**Diego Rodríguez**

[![GitHub](https://img.shields.io/badge/GitHub-Profile-181717?style=for-the-badge\&logo=github\&logoColor=white)](https://github.com/dewstouh) [![Website](https://img.shields.io/badge/Website-justdiego.com-FF6B6B?style=for-the-badge\&logo=firefox\&logoColor=white)](https://justdiego.com)

***

#### 📚 Related Guides

* [🖥️ Coolify Setup](../uptime-status/)
* [📧 Mail Server Setup](../mail/)
* [🔙 Back to Main Guide](../../../)

***

#### 🆘 Need Help?

* [🔧 Troubleshooting Guide](../../../guides/troubleshooting/)
* [🔥 Firewall Configuration](../../../guides/firewall/)
* [📁 Backup & Recovery](../../../guides/backup/)

***

_This guide is part of the **Just Diego Infra** project. Monitor everything, break nothing!_
