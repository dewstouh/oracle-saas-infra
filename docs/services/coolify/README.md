# 🖥️ Webpanel Setup

## 🖥️ Coolify Setup

#### Self-hosted VPS Web Panel

<p align="center"><a href="https://coolify.io/"><img src="https://img.shields.io/badge/Coolify-Self_Hosted-6366F1?style=for-the-badge&#x26;logo=coolify&#x26;logoColor=white" alt="Coolify"> </a><a href="https://cloud.oracle.com/"><img src="https://img.shields.io/badge/Oracle_Cloud-Compatible-F80000?style=for-the-badge&#x26;logo=oracle&#x26;logoColor=white" alt="Oracle Cloud"> </a><a href="https://www.docker.com/"><img src="https://img.shields.io/badge/Docker-Required-2496ED?style=for-the-badge&#x26;logo=docker&#x26;logoColor=white" alt="Docker"></a><br><a href="https://ubuntu.com/"><img src="https://img.shields.io/badge/Ubuntu-22.04_LTS-E95420?style=for-the-badge&#x26;logo=ubuntu&#x26;logoColor=white" alt="Ubuntu"> </a><a href="https://coolify.io/"><img src="https://img.shields.io/badge/License-Free-00D4AA?style=for-the-badge&#x26;logo=opensourceinitiative&#x26;logoColor=white" alt="License"></a></p>

![Coolify Banner](https://i.imgur.com/bYbu313.png)

**Official Website:** [coolify.io](https://coolify.io/) | **Documentation:** [coolify.io/docs](https://coolify.io/docs/get-started/installation)

***

### 📋 Overview

This guide will help you set up a **self-hosted Coolify panel**, a web dashboard for managing your VPS, its services, status, and way more, on Oracle Cloud. It covers the necessary steps to install and configure Coolify, as well as how to access it.

> 🎯 **Target Environment**: Oracle Cloud instance running Ubuntu Server 22.04 LTS

### Why to use a Webpanel?

A web panel like Coolify allows you to manage your VPS and its services easily, **without needing to use the command line**. It provides a **user-friendly interface** for **managing your services**, **monitoring your server's health**, and **accessing logs** and other information.

While it can be a pain to set up a whole panel like this, **it is worth it in the long run**, as it allows you to **manage your server more easily and efficiently**. As for scalability, it is the best choice any server manager can make, as it **allows you to manage multiple servers from a single interface**, even **share resources, services or applications** between them. It also allows **load balancing**, and the best part of it, is that **its free for everyone**

### What does it mean with self-hosted?

Self-hosted means that you will be running the Coolify panel on your own server, rather than using a third-party service. This gives you full control over your data and the ability to customize the panel to your needs. It also allows you to run the panel on your own hardware, which can be more cost-effective in the long run.

### Prerequisites

* Server with SSH access (Oracle Cloud instance).
* AMD64 or ARM64 architecture
* 2 cores of CPU
* 2 GB of RAM
* 30 GB of free space

Coolify may function properly on servers with lower specs than those mentioned above, but its to have slightly higher than minimum requirements.

> It’s best to use a fresh server for Coolify to avoid any conflicts with existing applications.

### Steps to Set Up Coolify

#### 1. Ensuring SSH Configuration

These settings need to be configured manually before running the Coolify installation script:

1.  **Edit SSH Configuration**

    ```bash
    nano /etc/ssh/sshd_config
    ```

    > Note: The `PermitRootLogin` option can be set to `yes`, `without-password`, or `prohibit-password`. For enhanced security, is recommended to use `prohibit-password`.
2.  **Ensure these settings are present:**

    ```bash
    PermitRootLogin prohibit-password
    PubkeyAuthentication yes
    ```
3.  **Restart SSH Service**

    ```bash
    sudo systemctl restart ssh
    ```

#### 2. Installing Coolify

1.  **Run the installation script:**

    ```bash
    curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
    ```
2.  **Access Coolify** After installation, the script will display your Coolify URL (e.g., `http://203.0.113.1:8000`). Visit this URL, and you'll be redirected to a registration page to create your first admin account.

    > If it does not work, make sure to open port 8000 in your firewall settings!
3.  **Create the Administrator Account** Once you've accessed the Coolify URL, you'll be prompted to create your first administrator account. Fill in the required information and click "Create Account".

    ![](https://i.imgur.com/A8beQ6M.png)

#### 3. Configuring Coolify

1. **Access the Dashboard**: After creating your account, you'll be redirected to the Coolify dashboard.
2.  **Add a Server**: You will be asked to add a server, where you can choose `Localhost` or `Remote Server` **Explanation**:

    * **Localhost:** The server where the Coolify is running on.
    * **A Remote Server:** A different server that you want to manage with Coolify through SSH, it can be hosted at home, or from any cloud provider.

    In this case, we are going to choose `Localhost` because we will be running the Coolify panel on the same server we are managing. ![](https://i.imgur.com/AoRILic.png)

    > NOTE: if you have two servers, you SHOULD only use `Localhost` for the panel and `Remote Server` for the rest of the services, this way, if any of your services crashes or fills the memory, your panel will still be accessible!
3. **Configure the Server**: After adding the server, you can configure it by clicking on the server name in the dashboard. Here you can set up various services, monitor resource usage, and manage applications. ![](https://i.imgur.com/xOecVL4.png)
4.  **Setting up the instance's domain**: We will set up the domain where the Coolify panel will be accessible. This is optional, but recommended for easier access.

    * Go to the `Settings` tab in the Coolify dashboard.
    * Under `Domain`, enter your domain name (e.g., `coolify.yourdomain.com`).
    * Click `Save`.

    ![](https://i.imgur.com/UrXAa4j.png)

    > Note: This will not make it work instantly, we first have to set up the proxies, as we are going to use cloudflare tunnels for maximum security, but it is good to have it ready.
5.  `OPTIONAL` **Setting up a Wildcard Domain with Cloudflare tunnels:**: This step is optional, but recommended for security and ease of access.

    A wilcard domain allows you to access all your services under the same domain, e.g., `service1.yourdomain.com`, `service2.yourdomain.com`, etc. This is useful for managing multiple services without needing to set up separate domains for each one, which takes a lot of time.

    * Go to your [cloudflare dashboard](https://dash.cloudflare.com/login).
    * Add your domain in case you don't have it ![](https://i.imgur.com/FIGA5dO.png)
    * Once it's added, go to the `Zero Trust` tab in the left sidebar. ![](https://i.imgur.com/iAscscY.png)
    * Click on `Networks` and then `Tunnels`. ![](https://i.imgur.com/VRHtmXT.png)
    * Click on `Create a tunnel`.
    * Enter a name for the tunnel (e.g., `coolify-tunnel`) and click `Next`.
    * Add your public hostname as a wildcard, in my case is `*.justdiego.com`, where the service port will be `http://localhost:80` ![](https://i.imgur.com/00Vc5HU.png)
    * Once you've created the tunnel, you will be given a command to run which will include the token of the tunnel, **copy the token only**. ![](https://i.imgur.com/AB5yPpx.png)
    * Save the token somewhere else (notepad for e.g).
    * Now, go to the overview of your tunnels and copy the **tunnel ID** ![](https://i.imgur.com/zKcQBZv.png)
    * Go to your domain DNS settings on cloudflare
    * Add a new DNS record with the following settings:
      * **Type:** CNAME
      * **Name:** `*` (for the wildcard)
      * **Target:** `your-tunnel-id.cfargotunnel.com` (replace `your-tunnel-id` with the actual tunnel ID you copied earlier)
      * **TTL:** Auto
      * **Proxy status:** Proxied (orange cloud icon) ![](https://i.imgur.com/SUZNitM.png)
    * Go back to the Coolify Dashboard
    * Go to the `Projects` tab
    * Click on `Add Project` ![](https://i.imgur.com/3VBUIUT.png)
    * Enter a name for the project (e.g., `Coolify Tunnel`)
    * Select `Environment` as `Production`
    * Add a new resource and look for `cloudflared` ![](https://i.imgur.com/NTJw7nC.png)
    * Go to the `Environment Variables` tab
    * Add the following variable:
      * **Name:** `CLOUDFLARE_TUNNEL_TOKEN`
      * **Value:** Paste the token you copied earlier from the Cloudflare tunnel setup. ![](https://i.imgur.com/einZrnM.png)
    * Click `Save` to create the project.
    * Once the project is created, click on `Deploy` to start the tunnel.
    * Go to the `Servers` tab in the Coolify dashboard.
    * Select your server
    * Add your wildcard domain under the `Wildcard Domain` setting ![](https://i.imgur.com/Tl9iRVw.png)
    * Click `Start proxy` to apply the changes.
    * Now you can access your Coolify panel at `http://coolify.yourdomain.com` or any other service you create under the wildcard domain.

#### 4. Accessing Coolify

* Open your web browser and go to the Coolify URL you set up (e.g., `http://coolify.yourdomain.com`).
* Log in with the administrator account you created.
* You will be redirected to the Coolify dashboard, where you can manage your server and its services.

### Services

Now that you have Coolify set up and running, you can start adding services to your server. Coolify makes it easy to deploy and manage various applications and services through its web interface.

If you want to add services now, you can explore the Coolify dashboard and deploy applications directly. Additionally, I have created comprehensive guides for setting up specific services that work well with Coolify:

#### Available Service Guides:

* [**Mail Server Setup**](../mail/) - Complete guide for setting up your own mail server
* [**Webmail Client**](../mail/webmail.md) - Web-based email client configuration
* [**Status/Uptime Monitoring**](../uptime-status/) - Monitor your services and server uptime

These guides will help you expand your server capabilities and create a complete self-hosted infrastructure solution.

***

### 👨‍💻 Author

**Diego Rodríguez**

[![GitHub](https://img.shields.io/badge/GitHub-Profile-181717?style=for-the-badge\&logo=github\&logoColor=white)](https://github.com/justdiego) [![Website](https://img.shields.io/badge/Website-justdiego.com-FF6B6B?style=for-the-badge\&logo=firefox\&logoColor=white)](https://justdiego.com)

***

#### 📚 Related Guides

* [📧 Mail Server Setup](../mail/)
* [📊 Status Page Monitoring](../uptime-status/)
* [🔙 Back to Main Guide](../../../)

***

#### 🆘 Need Help?

* [🔧 Troubleshooting Guide](../../../guides/troubleshooting/)
* [🔥 Firewall Configuration](../../../guides/firewall/)
* [📁 Backup & Recovery](../../../guides/backup/)

***

_This guide is part of the **Just Diego Infra** project. If you encounter issues, refer to the troubleshooting section or create an issue._
