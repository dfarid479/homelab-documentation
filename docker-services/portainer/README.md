# Portainer

Docker container management UI with full control over stacks, containers, images, volumes, and networks.

## Configuration

| Property | Value |
|----------|-------|
| **Image** | `portainer/portainer-ce:latest` |
| **Port** | 9000 (HTTP), 9443 (HTTPS) |
| **Data Path** | `/mnt/main-storage/portainer` |

## Docker Compose

```yaml
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /mnt/main-storage/portainer:/data
    logging:
      driver: gelf
      options:
        gelf-address: "udp://10.0.0.X:12201"
        tag: "portainer"
    networks:
      - homelab
```

## Initial Setup

1. Access `http://10.0.0.X:9000`
2. Create admin account (first login)
3. Select "Docker Standalone"
4. Begin managing containers

## Key Features

- **Stacks**: Deploy multi-container applications
- **Containers**: Start, stop, restart, view logs
- **Images**: Pull, remove, inspect images
- **Volumes**: Manage persistent storage
- **Networks**: Configure container networking
- **Settings**: Backup/restore configuration

## Security Notes

- Admin password required
- Disable public access (use Tailscale VPN)
- Docker socket mounted read-only
- Regular backups of `/data` volume

## Backup

Configuration stored in `/mnt/main-storage/portainer`:
```bash
# Backup
tar -czf portainer-backup.tar.gz /mnt/main-storage/portainer

# Restore
tar -xzf portainer-backup.tar.gz -C /
```
