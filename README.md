# justdiego-infra

> **Production-ready modular infrastructure for SaaS, personal projects and real-world deployment.**

This repository contains the actual infrastructure code that powers all my projects and status pages. It’s fully documented, portable and designed to be scalable and robust.

---

## 🚀 What’s Included?

- **Centralized Status Page**  
  - Powered by [Cachet](https://github.com/cachethq/cachet)  
  - Multi-project/component support  
  - Used for:  
    - [status.justdiego.com](https://status.justdiego.com)  

- **Monitoring**  
  - [Uptime Kuma](https://github.com/louislam/uptime-kuma) ready (integration scripts included)
  - Real-time uptime and incident automation (optional)

- **Proxy / Reverse Proxy**  
  - Traefik/Nginx configs for SSL, redirects and multi-domain setup

- **Automated Backups**  
  - Scripts for persistent volume and DB backup

- **Plug & Play Deployment**  
  - Docker Compose for full infra  
  - Coolify-ready

- **DevOps Utilities**  
  - Migration scripts  
  - Restore scripts  
  - Example CI/CD setup

---

## ⚡️ Quick Start

1. **Clone this repo:**
    ```bash
    git clone https://github.com/dewstouh/justdiego-infra.git
    cd justdiego-infra
    ```

2. **Copy and edit environment variables:**
    ```bash
    cp .env.example .env
    nano .env
    ```

3. **Start the full stack:**
    ```bash
    docker-compose up -d
    ```

4. **Access your status page:**  
   Visit `https://status.justdiego.com` (or your assigned domain).

---

## 🌐 Domains & Multi-Project Setup

- All status pages and monitoring dashboards are deployed from a **central stack**.
- Custom domains for each project (e.g., `status.codebeans.com`) are set up as redirects or proxies to the relevant section/group.
- Fully documented in `/docs/domains.md`.

---

## 🛡️ Production-Ready

- Volumes for data persistence (zero data loss on migration or update)
- Backups automated & documented
- One-command migration and restore

---

## 📸 Screenshots

> [TODO]

---

## 🧩 Extensible

- Add new services by editing `docker-compose.yaml` and config files
- Modular: can include new monitoring, CI/CD, proxies, or databases as needed

---

## 💡 Why Open Source?

I maintain this repository public so anyone (recruiters, companies, or developers) can see, test, or adapt a real, working SaaS-level infrastructure.  
**See it in action:**  
- [status.justdiego.com](https://status.justdiego.com)

---

## 🤝 Contributions

Suggestions, issues, and PRs welcome!  
Feel free to fork and adapt this stack for your own SaaS/product.

---
