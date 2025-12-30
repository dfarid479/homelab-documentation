# Immich

Self-hosted photo and video backup solution with mobile apps.

## Configuration

| Property | Value |
|----------|-------|
| **Port** | 2283 |
| **Upload Path** | `/mnt/main-storage/immich/upload` |
| **DB Path** | `/mnt/main-storage/immich/postgres` |

## Docker Compose

```yaml
services:
  immich-server:
    image: ghcr.io/immich-app/immich-server:release
    container_name: immich-server
    restart: unless-stopped
    ports:
      - "2283:3001"
    volumes:
      - /mnt/main-storage/immich/upload:/usr/src/app/upload
    environment:
      - DB_HOSTNAME=immich-postgres
      - DB_DATABASE_NAME=immich
      - DB_USERNAME=immich
      - DB_PASSWORD=your-password
      - REDIS_HOSTNAME=immich-redis
    depends_on:
      - immich-postgres
      - immich-redis
    logging:
      driver: gelf
      options:
        gelf-address: "udp://10.0.0.X:12201"
        tag: "immich"
    networks:
      - homelab

  immich-postgres:
    image: tensorchord/pgvecto-rs:pg14-v0.2.0
    container_name: immich-postgres
    restart: unless-stopped
    environment:
      POSTGRES_PASSWORD: your-password
      POSTGRES_USER: immich
      POSTGRES_DB: immich
    volumes:
      - /mnt/main-storage/immich/postgres:/var/lib/postgresql/data
    networks:
      - homelab

  immich-redis:
    image: redis:7-alpine
    container_name: immich-redis
    restart: unless-stopped
    networks:
      - homelab
```

## Features

- Automatic mobile photo backup
- Facial recognition
- Object detection
- Map view
- Album sharing
- Timeline view

## Mobile Apps

Available on iOS and Android app stores.
