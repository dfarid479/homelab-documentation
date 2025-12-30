# Vaultwarden

Lightweight self-hosted Bitwarden server for password management.

## Configuration

| Property | Value |
|----------|-------|
| **Image** | `vaultwarden/server:latest` |
| **Port** | 8000 |
| **Data Path** | `/mnt/main-storage/vaultwarden` |

## Docker Compose

```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    ports:
      - "8000:80"
    volumes:
      - /mnt/main-storage/vaultwarden:/data
    environment:
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=false
      - ADMIN_TOKEN=your-admin-token
    logging:
      driver: gelf
      options:
        gelf-address: "udp://10.0.0.X:12201"
        tag: "vaultwarden"
    networks:
      - homelab
```

## Security

- Signups disabled (invite-only)
- Admin panel protected with token
- SSL/TLS via Nginx Proxy Manager
- Regular database backups

## Access

- Web vault: `https://vault.your-domain.net`
- Admin panel: `https://vault.your-domain.net/admin`
- Browser extensions available
- Mobile apps available
