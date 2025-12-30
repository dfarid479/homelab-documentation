# Nginx Proxy Manager

Reverse proxy manager with SSL certificate automation and user-friendly web interface.

## Configuration

| Property | Value |
|----------|-------|
| **Image** | `jc21/nginx-proxy-manager:latest` |
| **Admin Port** | 9011 |
| **HTTP Port** | 80 |
| **HTTPS Port** | 443 |
| **Data Path** | `/mnt/main-storage/nginx-proxy-manager` |

## Docker Compose

```yaml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "9011:81"
    volumes:
      - /mnt/main-storage/nginx-proxy-manager/data:/data
      - /mnt/main-storage/nginx-proxy-manager/letsencrypt:/etc/letsencrypt
    logging:
      driver: gelf
      options:
        gelf-address: "udp://10.0.0.X:12201"
        tag: "nginx-proxy-manager"
    networks:
      - homelab
```

## Initial Setup

1. Access `http://10.0.0.X:9011`
2. Default credentials:
   - Email: `admin@example.com`
   - Password: `changeme`
3. Change credentials immediately
4. Configure Cloudflare API for DNS challenge

## SSL Certificate Management

### Cloudflare DNS Challenge

1. **SSL Certificates** → **Add SSL Certificate**
2. Select **Let's Encrypt**
3. Choose **Use a DNS Challenge**
4. Provider: **Cloudflare**
5. Enter Cloudflare credentials (API token)
6. Add domains

### Wildcard Certificates

Use DNS challenge for wildcard certs:
- `*.your-domain.net`
- `your-domain.net`

## Proxy Hosts

### Example Configuration

**Service**: Plex  
**Domain**: `plex.your-domain.net`  
**Forward Hostname**: `10.0.0.X`  
**Forward Port**: `32400`  
**SSL**: Enabled with Cloudflare cert  
**Force SSL**: Enabled  
**HTTP/2**: Enabled  
**HSTS**: Enabled

## Services Proxied

- Plex
- Immich
- Overseerr
- Vaultwarden
- FileBrowser

## Security

- Admin panel behind Tailscale VPN
- Strong admin password
- Cloudflare API token (not Global API Key)
- Rate limiting on sensitive endpoints
- Regular certificate renewal monitoring
