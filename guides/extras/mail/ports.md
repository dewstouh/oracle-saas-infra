# Opening Ports for Mail Service in Oracle Cloud

When setting up a mail service in Oracle Cloud, you may need to open specific ports to allow email traffic. Below are the common ports used for mail services:

- **SMTP (Simple Mail Transfer Protocol)**: 
  - Port 25: Standard port for sending emails.
  - Port 587: Used for secure SMTP submission with STARTTLS.
  - Port 465: Used for secure SMTP over SSL.
  - Port 993: Used for secure IMAP over SSL.
  - Port 995: Used for secure POP3 over SSL.
  - Port 143: Used for IMAP (Internet Message Access Protocol).
  - Port 110: Used for POP3 (Post Office Protocol).
  - Port 80: Used for webmail access (HTTP).
  - Port 443: Used for secure webmail access (HTTPS).
  - Port 8080: Alternative port for webmail access.
  - Port 8443: Alternative port for secure webmail access.
  - Port 465: Used for secure SMTP over SSL.
  - Port 587: Used for secure SMTP submission with STARTTLS.


In this case, we just need to open the following ports:
- Port 25: For SMTP
- Port 587: For secure SMTP submission with STARTTLS
- Port 465: For secure SMTP over SSL
- Port 993: For secure IMAP over SSL
- Port 995: For secure POP3 over SSL
- Port 143: For IMAP
- Port 110: For POP3
- Port 80: For webmail access (HTTP)
- Port 443: For secure webmail access (HTTPS)

## Opening the ports
Go to the [Oracle Cloud Console](https://cloud.oracle.com/), navigate to your instance, and follow these steps:
1. **Navigate to the Networking section**: Click on "Networking" in the left sidebar.
2. **Select Virtual Cloud Networks (VCN)**: Choose the VCN associated with your instance.
3. **Go to Security Lists**: Click on "Security Lists" under the VCN details.
![](https://imgur.com/dTDDNBl.png)
4. **Add Ingress Rules**: Click on "Add Ingress Rules" and specify the following details for each port:
   - **Source CIDR**: Use `0.0.0.0/0` to allow traffic from any IP address, or specify a more restrictive CIDR if needed.
   - **IP Protocol**: Select `TCP`.
   - **Destination Port Range**: Enter the port number `25, 587, 465, 993, 995, 143, 110, 80, 443`.
![](https://imgur.com/UEnYUw2.png)
5. **Save the Rules**: Click "Add Ingress Rule" to save each rule.
6. **Setting up the firewall**: Ensure that your instance's firewall allows traffic on these ports. You may need to configure the firewall settings on your instance's operating system as well.
- ```sudo ufw allow 25/tcp```
- ```sudo ufw allow 587/tcp```
- ```sudo ufw allow 465/tcp```
- ```sudo ufw allow 993/tcp```
- ```sudo ufw allow 995/tcp```
- ```sudo ufw allow 143/tcp```
- ```sudo ufw allow 110/tcp```
- ```sudo ufw allow 80/tcp```
- ```sudo ufw allow 443/tcp```
- ```sudo ufw reload```

7. **Verify that the firewall has those rules added**: 
- ```sudo ufw status numbered```
It should print something like this:
```
[28] 25 (v6)                    ALLOW IN    Anywhere (v6)             
[29] 465 (v6)                   ALLOW IN    Anywhere (v6)             
[30] 487 (v6)                   DENY IN     Anywhere (v6)             
[31] 587 (v6)                   ALLOW IN    Anywhere (v6)             
[32] 143 (v6)                   ALLOW IN    Anywhere (v6)             
[33] 993 (v6)                   ALLOW IN    Anywhere (v6)             
[34] 110 (v6)                   ALLOW IN    Anywhere (v6)             
[35] 995 (v6)                   ALLOW IN    Anywhere (v6)             
[36] 993/tcp (v6)               ALLOW IN    Anywhere (v6)             
[37] 587/tcp (v6)               ALLOW IN    Anywhere (v6)             
[38] 25/tcp (v6)                ALLOW IN    Anywhere (v6)             
[39] 456/tcp (v6)               ALLOW IN    Anywhere (v6)             
[40] 995/tcp (v6)               ALLOW IN    Anywhere (v6)             
[41] 143/tcp (v6)               ALLOW IN    Anywhere (v6)             
[42] 110/tcp (v6)               ALLOW IN    Anywhere (v6)             
[43] 465/tcp (v6)               ALLOW IN    Anywhere (v6)             
[44] 4190/tcp (v6)              ALLOW IN    Anywhere (v6) 
```

---

<!-- FOOTER_START -->
By Diego Rodriguez
- ![GitHub](https://img.shields.io/badge/GitHub-dewstouh-181717?style=flat-square&logo=github)
<!-- FOOTER_END -->
