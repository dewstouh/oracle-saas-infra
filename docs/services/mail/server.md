# Mail Server (Stalwart) + Webmail (Snappymail) Setup Guide
> This guide is for setting up a mail server using Stalwart and a webmail client using Snappymail WITH coolify, if you don't have coolify installed, please follow the [coolify installation guide](https://coolify.io/docs/installation/) or just check the official documentation.
## Installation
We are going to install the mail server using Docker Compose, so make sure you have Docker and Docker Compose installed on your server. If you don't have them installed, please follow the [Docker installation guide](https://docs.docker.com/engine/install/) and the [Docker Compose installation guide](https://docs.docker.com/compose/install/).

## Prerequisites
- A domain name (e.g., `justdiego.com`)
- A server with Docker and Docker Compose installed
- Ports 25, 587, 465, 143, 993, 4190, 110, and 995 open on your server (If you are using Oracle Cloud, follow [this guide](ports.md))

### 1. Create a new project in Coolify and name it `Mail`.
![](https://imgur.com/q40fVIV.png)
### 2. Create a new service in the project and name it `Mail Server`.
Use the following docker compose file (replace justdiego.com with your domain name):
This will install the Stalwart mail server, which is a modern and secure mail server that supports SMTP, IMAP, and JMAP protocols. It also supports TLS encryption and has a web interface for managing the mail server.
```yaml
services:
  stalwart-mail:
    image: 'stalwartlabs/stalwart:latest'
    container_name: stalwart
    networks:
      - coolify
    ports:
      - '25:25'
      - '587:587'
      - '465:465'
      - '143:143'
      - '993:993'
      - '4190:4190'
      - '110:110'
      - '995:995'
    volumes:
      - '/var/lib/stalwart:/opt/stalwart'
      - '/etc/localtime:/etc/localtime:ro'
      - '/data/coolify/certs:/data/certs:ro'
    labels:
      - traefik.enable=true
      - 'traefik.http.routers.mailserver.rule=Host(`mail.justdiego.com`) || Host(`autodiscover.justdiego.com`) || Host(`autoconfig.justdiego.com`) || Host(`mta-sts.justdiego.com`) || Host(`mx.justdiego.com`) || Host(`smtp.justdiego.com`) || Host(`pop.justdiego.com`) || Host(`imap.justdiego.com`)'
      - traefik.http.routers.mailserver.entrypoints=http
      - traefik.http.routers.mailserver.service=mailserver
      - traefik.http.services.mailserver.loadbalancer.server.port=8080
      - traefik.http.routers.mailserver.tls.certresolver=letsencrypt
      - traefik.http.routers.mailserver.tls=true
      - 'traefik.http.routers.mailserver.tls.domains[0].main=mail.justdiego.com'
      - 'traefik.http.routers.mailserver.tls.domains[0].sans=autodiscover.justdiego.com,autoconfig.justdiego.com,mta-sts.justdiego.com,mx.justdiego.com,smtp.justdiego.com,pop.justdiego.com,imap.justdiego.com'
      - 'traefik.tcp.routers.smtp.rule=HostSNI(`*`)'
      - traefik.tcp.routers.smtp.entrypoints=smtp
      - traefik.tcp.routers.smtp.service=smtp
      - traefik.tcp.services.smtp.loadbalancer.server.port=25
      - traefik.tcp.services.smtp.loadbalancer.proxyProtocol.version=2
      - 'traefik.tcp.routers.jmap.rule=HostSNI(`*`)'
      - traefik.tcp.routers.jmap.tls.passthrough=true
      - traefik.tcp.routers.jmap.entrypoints=https
      - traefik.tcp.routers.jmap.service=jmap
      - traefik.tcp.services.jmap.loadbalancer.server.port=443
      - traefik.tcp.services.jmap.loadbalancer.proxyProtocol.version=2
      - 'traefik.tcp.routers.smtps.rule=HostSNI(`*`)'
      - traefik.tcp.routers.smtps.tls.passthrough=true
      - traefik.tcp.routers.smtps.entrypoints=smtps
      - traefik.tcp.routers.smtps.service=smtps
      - traefik.tcp.services.smtps.loadbalancer.server.port=465
      - traefik.tcp.services.smtps.loadbalancer.proxyProtocol.version=2
      - 'traefik.tcp.routers.imaps.rule=HostSNI(`*`)'
      - traefik.tcp.routers.imaps.tls.passthrough=true
      - traefik.tcp.routers.imaps.entrypoints=imaps
      - traefik.tcp.routers.imaps.service=imaps
      - traefik.tcp.services.imaps.loadbalancer.server.port=993
      - traefik.tcp.services.imaps.loadbalancer.proxyProtocol.version=2
    tty: true
    stdin_open: true
    restart: always
volumes:
  data: null
networks:
  coolify:
    external: true
```
### 3. Go to the Service Settings tab and set the domain to access the mail server panel (e.g., `mail.justdiego.com`).
![](https://imgur.com/7V8fZly.png)


### 4. Log in to the mail server panel using the credentials you can find under the "log" tab in Coolify.
![](https://imgur.com/SbutJKQ.png)
![](https://imgur.com/SR61Ktf.png)

### 5. Add a new domain in the mail server panel (e.g., `justdiego.com`).
- Save changes
![](https://imgur.com/qbVgpT3.png)
### 6. Add a new user under the domain you just created
- Save changes and reload
![](https://imgur.com/XmjXE94.png)
### 7. Set the hostname to `mail.yourdomain.com` on the Stalwart Network Settings
- Save changes and reload
![](https://imgur.com/lCz1FaR.png)
### 8. Add an ACME Provider in the Stalwart panel
- Go to the "ACME Providers" tab and add a new provider.
- Set the ID to `Auth`
- Leave the rest blank
![](https://imgur.com/I7cBpds.png)

## [OPTIONAL] 9. SMTP Relay

Not all VPS providers allow you to send emails directly from your server, some of them block the SMTP ports (25, 587, 465) to prevent spam. If you are using a VPS provider that blocks these ports which is what happens in my case with Oracle, you can use an SMTP relay to send emails instead.

### 10. Registering in a SMTP Relay Service
You can use any SMTP relay service, but I recommend using [SMTP2GO](https://www.smtp2go.com/) or [Brevo](https://brevo.com/). Both of them offer a free plan that allows you to send up to 1,000 emails per month.

Once you have registered in the SMTP relay service, you will need to get the SMTP credentials (username, password, host, port) to configure it in the Stalwart panel.

- Go to `Senders, Domains & Dedicated IPs`
![](https://imgur.com/2Y9ChPe.png)
- Click on `Add Domain`
![](https://imgur.com/W40Punl.png)
- Authenticate your domain by adding the DNS records provided by the SMTP relay service to your domain's DNS settings. This usually includes SPF, DKIM, and DMARC records.
![](https://imgur.com/qTjmCUs.png)

- Once everything is set up, you will see your domain in the list of domains.
![](https://imgur.com/im9LBMs.png)

### 11. Getting the SMTP Credentials
- Go to the `SMTP & API Credentials` tab.
![](https://imgur.com/WrRwaL7.png)

- Copy the SMTP credentials (username, password, host, port) and save them for later.
![](https://imgur.com/YHXETAO.png)
- & the password
![](https://imgur.com/ZLYTiqy.png)

### 12. Configuring the SMTP Relay in Stalwart
- Go to the `SMTP Relay Hosts` tab in the Stalwart panel.
- Click on `+ Create Host`
![](https://imgur.com/UZbUAwX.png)
- Fill in the form with the SMTP credentials you copied earlier.
![](https://imgur.com/Q13hgYe.png)
- Click on `Save and Reload`.

### 13. Setting the SMTP Relay as the Default Host
- Go to the `Outbound Settings` tab in the Stalwart panel.
- Go to the `Routing` section.
- Add the SMTP relay host you just created as the default host by setting the ID you specified before.
![](https://imgur.com/l54pUEt.png)

### 14. Disabling DANE and MTA-STS
Stalwart uses DANE and MTA-STS to secure the SMTP connections, but since we are using an SMTP relay, we need to disable these features.
![](https://imgur.com/tx88LaG.png)


### 15. Setting the Default TLS Certificate
Since we are using traefik, we need to configure it along with Stalwart to use the default TLS certificate provided by Stalwart.

- Go to `Proxy` and add this on the Traefik configuration file:
```yaml
  traefik-certs-dumper:
    image: ghcr.io/kereis/traefik-certs-dumper:latest
    container_name: traefik-certs-dumper
    restart: unless-stopped
    depends_on:
      - traefik
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /data/coolify/proxy:/traefik:ro
      - /data/coolify/certs:/output
```

![](https://i.imgur.com/H97Roj2.png)

- Restart the proxy server
- Use the TLS certificate in Stalwart
![](https://i.imgur.com/wxfdkDP.png)



### 16. Save changes and restart the whole service.
- Now we are going to restart the whole service to forcefully apply the changes. Since im using Coolify, i'll go to the Mailserver service tab and click on "Restart".

### 17. Setting up DNS Records
You will need to set up the following DNS records for your domain to ensure that your mail server works correctly:
- **MX Record**: Pointing to `mail.yourdomain.com` with a priority of `10`.
- **SPF Record**: `yourdomain.com` `v=spf1 include:spf.brevo.com mx ~all`.
- **DKIM Record**: Already added by the SMTP server.
- **DMARC Record**: `v=DMARC1; p=none; rua=mailto:
- **CNAME Records**: 
  - `autodiscover.yourdomain.com` pointing to `mail.yourdomain.com`
  - `autoconfig.yourdomain.com` pointing to `mail.yourdomain.com`
  - `mta-sts.yourdomain.com` pointing to `mail.yourdomain.com`
  - `mx.yourdomain.com` pointing to `mail.yourdomain.com`
  - `smtp.yourdomain.com` pointing to `mail.yourdomain.com`
  - `pop.yourdomain.com` pointing to `mail.yourdomain.com`
  - `imap.yourdomain.com` pointing to `mail.yourdomain.com`
- **A Record**: `mail.yourdomain.com` pointing to your server's IP address.
![](https://imgur.com/5bLB71W.png)

## 18. Testing the Mail Server
In order to test the mail server, you can use the following tools:
- [Mail Tester](https://www.mail-tester.com/): This tool will check your mail server configuration and give you a score based on the best practices.
- [MX Toolbox](https://mxtoolbox.com/): This tool will check your mail server's DNS records and give you a report on the configuration.
- [Gmail](https://mail.google.com/): You can send an email to your Gmail account and check if it arrives correctly. If it does, then your mail server is working correctly.


But im going to use [Snappymail](https://snappymail.eu/) which is a webmail client that allows you to access your mail server from the web. If you want to know how to install it, [follow this guide](webmail.md).

## 19. Setting up the Mail Client
Since we added an autoconfig and autodiscover CNAME records, our mail client should be able to automatically configure the settings for our mail server. If it doesn't, you can use the following settings:
- **Incoming Mail Server (IMAP)**:
  - Server: `mail.yourdomain.com`
  - Port: `993`
  - Security: `SSL/TLS`
  - Username: `your email address`
  - Password: `your email password`
- **Outgoing Mail Server (SMTP)**:
- Server: `mail.yourdomain.com`
  - Port: `587`
  - Security: `SSL/TLS`
  - Username: `your email address`
  - Password: `your email password`

![](https://imgur.com/yYCk8BS.png)
![](https://imgur.com/ldXjYqX.png)

## 20. Logging in to the Mail Client with the Account we created before
- Go to the webmail client URL (e.g., `mail.justdiego.com`)
- Enter your email address and password.
- You should be able to access your email account and send/receive emails.
![](https://imgur.com/a8ZpK6J.png)

If everything is working correctly, you should be able to send and receive emails from your mail server using the webmail client.

![](https://imgur.com/Cz1dxnx.png)

---

<!-- FOOTER_START -->
By Diego Rodriguez
- ![GitHub](https://img.shields.io/badge/GitHub-dewstouh-181717?style=flat-square&logo=github)
<!-- FOOTER_END -->
