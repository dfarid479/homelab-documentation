# Network Architecture

Complete network topology and configuration for the homelab.

## Network Topology

```
Internet
    │
    └─── ISP Router (192.168.1.1)
         │
         └─── Dell T3620 TrueNAS (10.0.0.X)
              ├─── Docker Bridge (172.17.0.0/16)
              │    ├─── Plex
              │    ├─── Immich  
              │    ├─── Graylog
              │    └─── [Other containers]
              │
              ├─── Tailscale VPN (100.x.x.x/10)
              │    └─── Remote access overlay network
              │
              └─── Pi-hole DNS (10.0.0.X:53)
                   └─── Upstream: Cloudflare (1.1.1.1)
```

## IP Addressing

| Network Segment | CIDR | Purpose |
|-----------------|------|---------|
| LAN | 10.0.0.0/24 | Primary network |
| Docker Bridge | 172.17.0.0/16 | Container networking |
| Tailscale | 100.x.x.x/10 | VPN overlay |

## DNS Architecture

### Pi-hole Configuration

- **Primary DNS**: 10.0.0.X:53
- **Upstream DNS**: 1.1.1.1, 1.0.0.1 (Cloudflare)
- **Ad Lists**: ~1M domains blocked
- **Local DNS**: Custom entries for internal services

## Reverse Proxy

### Nginx Proxy Manager

Public services accessible via:
- `*.your-domain.net` → Internal services
- SSL certificates via Cloudflare DNS challenge
- Automatic renewal

### Proxied Services

| Service | Internal | External |
|---------|----------|----------|
| Plex | 10.0.0.X:32400 | plex.waltbyte.net |
| Immich | 10.0.0.X:8082 | photos.waltbyte.net |
| Vaultwarden | 10.0.0.X:8200 | vault.waltbyte.net |
| Overseerr | 10.0.0.X:5055 | requests.waltbyte.net |
| FileBrowser | 10.0.0.X:8084 | files.waltbyte.net |
| qBittorrent | 10.0.0.X:8083 | downloads.waltbyte.net |
| Pi-hole | 10.0.0.X:8080 | dns.waltbyte.net |
| CouchDB (LiveSync) | 10.0.0.X:5984 | sync.waltbyte.net |

## VPN Access

### Tailscale

- **Subnet routing**: 10.0.0.0/24 advertised
- **Exit node**: Optional
- **ACLs**: Restrictive policies
- **Access**: Remote administration and services

## Firewall Rules

### TrueNAS Host

- Allow: 22 (SSH - VPN only)
- Allow: 80, 443 (HTTP/HTTPS - proxied services)
- Allow: Tailscale (41641/UDP)
- Drop: All other incoming

## Security

- No direct internet exposure for admin panels
- Tailscale VPN required for management
- Pi-hole blocks malicious domains
- Regular security updates
- SSL/TLS for all public services
- Rate limiting on proxy

## Monitoring

- Graylog: Centralized logging
- Uptime Kuma: Service availability
- TrueNAS: Disk and network metrics
- Pi-hole: DNS query statistics
