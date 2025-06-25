<div align="center">

# 📧 Mail Server Setup
### Complete mail server solution for Oracle Cloud

<p align="center">
  <a href="https://cloud.oracle.com/">
    <img src="https://img.shields.io/badge/Oracle_Cloud-Compatible-F80000?style=for-the-badge&logo=oracle&logoColor=white" alt="Oracle Cloud">
  </a>
  <a href="https://stalw.art/">
    <img src="https://img.shields.io/badge/Stalwart-Mail_Server-2E8B57?style=for-the-badge&logo=mail&logoColor=white" alt="Stalwart">
  </a>
  <a href="https://snappymail.eu/">
    <img src="https://img.shields.io/badge/SnappyMail-Webmail-FF6B35?style=for-the-badge&logo=mail&logoColor=white" alt="SnappyMail">
  </a>
  <br>
  <img src="https://img.shields.io/badge/SMTP-Enabled-4CAF50?style=for-the-badge&logo=gmail&logoColor=white" alt="SMTP">
  <img src="https://img.shields.io/badge/IMAP-Enabled-2196F3?style=for-the-badge&logo=thunderbird&logoColor=white" alt="IMAP">
</p>

![Mail Server Banner](https://i.imgur.com/pKKT8TT.png)

</div>

---

## 📋 Overview

This guide will help you set up a **complete mail server** on Oracle Cloud, including webmail, IMAP, SMTP, and more. It covers the necessary ports to open and how to configure them.

> ⚠️ **Oracle Cloud Challenge**: Oracle cloud instances have port 25 locked by default, but we will learn how to overcome this problem.

# Steps to Set Up a Mail Server
1. [Opening the required ports](#ports-to-open)
  **Opening the necessary ports for mail services in Oracle Cloud.**
    - Before you start, ensure you have the necessary ports open in your Oracle Cloud instance, otherwise, you won't be able to send or  receive emails.
    ![](https://i.imgur.com/Q5iWlq8.png)

---

2. [Installing the Mail Server](#installing-mail-server)
    **Installing Stalwart mail server software and configuring it.**
    - We will use stalwart as our mail server software, which is a lightweight and efficient mail server under an SMTP layer so that we are able to send emails without needing the port 25 from Oracle Cloud.
    ![](https://i.imgur.com/Uvnwrww.png)



--- 

3. [Setting up a Webmail Client](#configuring-webmail)
    **Setting up webmail for easy access to your emails.**
    - We will use Snappymail as our webmail client, which is a simple and user-friendly webmail interface and also allows us to use multiple clients and domains at the same time.
    ![](https://i.imgur.com/uz0PChZ.png)

---

<div align="center">

## 👨‍💻 Author

**Diego Rodríguez**

[![GitHub](https://img.shields.io/badge/GitHub-Profile-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/justdiego)
[![Website](https://img.shields.io/badge/Website-justdiego.com-FF6B6B?style=for-the-badge&logo=firefox&logoColor=white)](https://justdiego.com)

---

### 📚 Related Guides
- [🖥️ VPS Panel Setup](../vps-panel/README.md)
- [📊 Status Page Setup](../uptime-status/README.md)
- [🔙 Back to Main Guide](../../../README.md)

---

*This guide is part of the **Just Diego Infra** project. If you encounter issues, refer to the troubleshooting section or create an issue.*

</div>