# Plex Media Server

Personal media streaming server for movies, TV shows, music, and photos.

## Configuration

| Property | Value |
|----------|-------|
| **Image** | `plexinc/pms-docker:latest` |
| **Port** | 32400 |
| **Media Path** | `/mnt/main-storage/media` |
| **Config Path** | `/mnt/main-storage/plex` |

## Docker Compose

```yaml
services:
  plex:
    image: plexinc/pms-docker:latest
    container_name: plex
    restart: unless-stopped
    network_mode: host
    environment:
      - TZ=America/New_York
      - PLEX_CLAIM=claim-xxxxx
    volumes:
      - /mnt/main-storage/plex:/config
      - /mnt/main-storage/media:/media
    logging:
      driver: gelf
      options:
        gelf-address: "udp://10.0.0.X:12201"
        tag: "plex"
```

## Setup

1. Get Plex claim token from https://www.plex.tv/claim/
2. Access `http://10.0.0.X:32400/web`
3. Complete setup wizard
4. Add media libraries

## Libraries

- Movies: `/media/movies`
- TV Shows: `/media/tv`
- Music: `/media/music`
- Photos: `/media/photos`

## Remote Access

Configured via Nginx Proxy Manager:
- Domain: `plex.your-domain.net`
- SSL enabled with Cloudflare cert
