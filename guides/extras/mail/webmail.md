# Webmail Client (SnappyMail) installation guide
I personally use SnappyMail as my webmail client because it is pretty fast, quick and easy to install, but you can use any other webmail client that supports IMAP and SMTP such as Roundcube and many others.

As i use `Coolify`, i will install it with a docker container, but you can also install it manually on your server.

## 1. Install SnappyMail using Docker Compose:

In the previous installation guide, we created a project named `Mail` in Coolify, now we have to add a resource which we are going to call `webmail`, and we will use the following `docker-compose.yml` file:

```yaml
services:
  snappymail:
    image: 'djmaze/snappymail:latest'
    container_name: snappymail
    restart: unless-stopped
    ports:
      - '8888:8888'
    volumes:
      - '/var/lib/snappymail:/var/lib/snappymail'
    environment:
      - TZ=Europe/Madrid
```

## 2. Setting up the domain name
You can use the same domain name you used for your mail server, or you can use a subdomain like `webmail.yourdomain.com`.

![](https://imgur.com/JS0G7v8.png)

## 3. Deploying the service
After adding the resource, you can deploy it by clicking on the "Deploy" button in Coolify. This will start the SnappyMail container and make it accessible via the specified port (8888 in this case). But it will be accessible on the domain name you set up in the previous step.

## 4. Getting the Administrator password
After the deployment is complete, you can get the administrator password by running the following command in your terminal:
```bash
docker exec snappymail cat /var/lib/snappymail/_data_/_default_/admin_password.txt
```

*Or you can just go to the Coolify's Terminal Docker panel and execute the command there*
![](https://imgur.com/vwj1pVC.png)

## 5. Accessing SnappyMail as an Administrator
- Open your web browser and go to `http://webmail.yourdomain.com?admin.
- Enter the administrator password you retrieved in the previous step.
- You will be redirected to the SnappyMail admin interface where you can manage your mail server
![](https://imgur.com/cdH3Shw.png)

## 6. Adding your mail server to SnappyMail
- Go to the "Domains" tab in the SnappyMail admin interface.
- Press on the "Add Domain" button.
- Enter your mail server domain (e.g., `mail.yourdomain.com`).
- Then press on `autoconfig`

If everything is set up correctly, SnappyMail will automatically detect your mail server settings and configure them for you.

![](https://imgur.com/y5ovnQn.png)

## 7. Recommended Extensions
SnappyMail supports various extensions that can enhance your webmail experience. Here are some recommended extensions
- **X-Originating-IP**: This extension adds the X-Originating-IP header to outgoing emails, which can help with email tracking and delivery.
- **Attachments force open**: This extension forces attachments to open in a new tab, making it easier to view files without downloading them.
- **Avatars**: This extension allows you to set avatars for your email accounts, making it easier to identify senders at a glance.
- **Have i been pwned**: This extension checks if your email address has been compromised in any data breaches, helping you to maintain security.
- **Search filters**: This extension allows you to create custom search filters for your emails, making it easier to find specific messages.
- **Send save in**: This extension automatically saves sent emails in the "Sent" folder, ensuring that you have a record of all your outgoing messages.
- **Two factor authentication**: This extension adds an extra layer of security to your webmail account by requiring a second form of verification when logging in.

![](https://imgur.com/zcAjzln.png)

> ## If you don't have a mail server, check out the [Mail Server Installation Guide](mailserver.md)

---

<!-- FOOTER_START -->
By Diego Rodriguez
- ![GitHub](https://img.shields.io/badge/GitHub-dewstouh-181717?style=flat-square&logo=github)
<!-- FOOTER_END -->
