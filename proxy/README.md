# Proxy & SSL Configuration

This directory contains all configurations related to reverse proxies and SSL termination for the infrastructure.

## What goes here?
- Traefik configuration files (static and dynamic configs)
- Nginx virtual host files
- Caddyfile or other reverse proxy configs
- SSL certificates or instructions for Let's Encrypt/ZeroSSL integrations

## Purpose

Centralizing your proxy configuration allows:
- Easy domain and SSL management across all services
- Clean separation between application logic and traffic management
- Fast migration of the entire infra stack (just move your proxy configs!)

## Typical Uses

- Routing domains (e.g., `status.justdiego.com`, `status.codebeans.com`) to the correct backend services
- Enabling HTTPS with Let's Encrypt or custom certs
- Redirects, rewrites, and access control

## Example Structure

```yaml
/proxy/
├── traefik/
│ ├── traefik.yml
│ └── dynamic/
│ └── rules.yml
├── nginx/
│ └── default.conf
└── caddy/
└── Caddyfile
```

## Resources

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Caddy Documentation](https://caddyserver.com/docs/)