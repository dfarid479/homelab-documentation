# Graylog

SIEM (Security Information and Event Management) for centralized log aggregation and analysis.

## Architecture

Graylog stack consists of:
- **Graylog Server**: Web UI and processing engine
- **MongoDB**: Configuration and metadata storage
- **OpenSearch**: Log storage and search

## Configuration

| Component | Port | Purpose |
|-----------|------|---------|
| Graylog Web | 9001 | Web interface |
| Graylog GELF | 12201/UDP | Log input |
| MongoDB | 27017 | Internal |
| OpenSearch | 9200 | Internal |

## Docker Compose

```yaml
services:
  mongodb:
    image: mongo:6
    container_name: graylog-mongodb
    restart: unless-stopped
    volumes:
      - /mnt/main-storage/graylog/mongodb:/data/db
    networks:
      - graylog-net

  opensearch:
    image: opensearchproject/opensearch:2
    container_name: graylog-opensearch
    restart: unless-stopped
    environment:
      - "OPENSEARCH_JAVA_OPTS=-Xms1g -Xmx1g"
      - "bootstrap.memory_lock=true"
      - "discovery.type=single-node"
      - "action.auto_create_index=false"
      - "plugins.security.disabled=true"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /mnt/main-storage/graylog/opensearch:/usr/share/opensearch/data
    networks:
      - graylog-net

  graylog:
    image: graylog/graylog:5.2
    container_name: graylog
    restart: unless-stopped
    depends_on:
      - mongodb
      - opensearch
    ports:
      - "9001:9000"
      - "12201:12201/udp"
    environment:
      GRAYLOG_PASSWORD_SECRET: "your-password-secret-here"
      GRAYLOG_ROOT_PASSWORD_SHA2: "your-sha2-hash-here"
      GRAYLOG_HTTP_EXTERNAL_URI: "http://10.0.0.X:9001/"
      GRAYLOG_MONGODB_URI: "mongodb://mongodb:27017/graylog"
      GRAYLOG_ELASTICSEARCH_HOSTS: "http://opensearch:9200"
    volumes:
      - /mnt/main-storage/graylog/data:/usr/share/graylog/data
    networks:
      - homelab
      - graylog-net

networks:
  graylog-net:
    driver: bridge
```

## Initial Setup

1. Generate password secret:
   ```bash
   pwgen -N 1 -s 96
   ```

2. Generate root password hash:
   ```bash
   echo -n "yourpassword" | sha256sum | cut -d" " -f1
   ```

3. Access `http://10.0.0.X:9001`
4. Login: `admin` / your_password

## Input Configuration

### GELF UDP Input

1. **System** → **Inputs**
2. **Select input**: GELF UDP
3. **Launch new input**
4. **Bind address**: 0.0.0.0
5. **Port**: 12201
6. **Title**: Docker GELF

## Container Logging

Add to all containers:
```yaml
logging:
  driver: gelf
  options:
    gelf-address: "udp://10.0.0.X:12201"
    tag: "container-name"
```

## Dashboards

Create dashboards for:
- Container health monitoring
- Error rate tracking
- Service performance metrics
- Security event correlation

## Retention Policy

Configure data retention:
1. **System** → **Indices**
2. Set rotation strategy: Time-based
3. Max number of indices: 30 (for 30 days)

## Security

- Change default admin password
- VPN-only access recommended
- Regular backups of MongoDB
- Monitor disk usage (logs grow quickly)
