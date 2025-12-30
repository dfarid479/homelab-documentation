# HOMELAB

Production-grade personal cloud infrastructure running on TrueNAS Scale with 20+ containerized services for digital forensics, media automation, and cybersecurity research.

## 🖥️ Hardware

| Component | Specification |
|-----------|--------------|
| **System** | Dell Precision T3620 |
| **CPU** | Intel Xeon E3-1245 v6 (4C/8T) |
| **RAM** | 64GB ECC DDR4 |
| **Storage** | 500TB ZFS Pool (RAIDZ2) |
| **Network** | Gigabit Ethernet |
| **UPS** | APC 1500VA |
| **OS** | TrueNAS Scale 24.10 |

## 🌐 Network Architecture

```
Internet
    │
    ├─── ISP Router (192.168.1.1)
    │
    ├─── Dell T3620 TrueNAS (10.0.0.X)
    │    ├─── Docker Bridge (172.17.0.0/16)
    │    ├─── Tailscale VPN (100.x.x.x)
    │    └─── Pi-hole DNS (10.0.0.X:53)
    │
    └─── Local Devices (10.0.0.0/24)
```

## 📦 Services

### Infrastructure Management
| Service | Purpose | Access |
|---------|---------|--------|
| [Portainer](docker-services/portainer/) | Container management | :9000 |
| [Nginx Proxy Manager](docker-services/nginx-proxy-manager/) | Reverse proxy | :9011 |
| [Tailscale](docker-services/tailscale/) | Zero-trust VPN | N/A |
| [Pi-hole](docker-services/pihole/) | DNS + Ad blocking | :8080 |

### Media & Entertainment
| Service | Purpose | Access |
|---------|---------|--------|
| [Plex](docker-services/plex/) | Media streaming | :32400 |
| [Immich](docker-services/immich/) | Photo management | :2283 |
| [Overseerr](docker-services/overseerr/) | Media requests | :5055 |
| [Radarr](docker-services/radarr/) | Movie automation | :7878 |
| [Sonarr](docker-services/sonarr/) | TV automation | :8989 |
| [Prowlarr](docker-services/prowlarr/) | Indexer manager | :9696 |
| [qBittorrent](docker-services/qbittorrent/) | Download client | :8083 |

### Security & Utilities
| Service | Purpose | Access |
|---------|---------|--------|
| [Vaultwarden](docker-services/vaultwarden/) | Password manager | :8000 |
| [FileBrowser](docker-services/filebrowser/) | Web file manager | :8082 |
| [Uptime Kuma](docker-services/uptime-kuma/) | Service monitoring | :3001 |
| [Watchtower](docker-services/watchtower/) | Update monitoring | N/A |

### Monitoring & Automation
| Service | Purpose | Access |
|---------|---------|--------|
| [Graylog](docker-services/graylog/) | SIEM & log aggregation | :9001 |
| [n8n](docker-services/n8n/) | Workflow automation | :5678 |

### Network Services
| Service | Purpose | Access |
|---------|---------|--------|
| [ProtonVPN](docker-services/protonvpn/) | VPN for specific containers | N/A |

## 🔧 Virtual Machines

- [Kali Linux](vms/kali-linux/) - Penetration testing & security research

## 📚 Documentation

- [Network Architecture](docs/NETWORK.md) - Complete network topology and configuration
- [Backup Strategy](scripts/backup/) - Automated backup procedures
- [Monitoring Scripts](scripts/monitoring/) - Health check automation
- [UPS Integration](scripts/ups/) - Power management

## 🚀 Quick Start

1. **Prerequisites**: TrueNAS Scale 24.10+, Docker installed
2. **Clone repository**: Documentation only (configs must be customized)
3. **Review service READMEs**: Each service has deployment instructions
4. **Configure environment variables**: Update IPs, domains, credentials
5. **Deploy services**: Use provided docker-compose files as templates

## 💾 Backup Strategy

| Type | Frequency | Retention | Location |
|------|-----------|-----------|----------|
| Container Configs | Daily | 30 days | Local ZFS snapshots |
| Service Data | Weekly | 60 days | Local + External HDD |
| Full System | Monthly | 180 days | External HDD |

## 🔒 Security

- **Network Segmentation**: Services isolated via Docker networks
- **VPN Access**: Tailscale for remote administration
- **DNS Filtering**: Pi-hole blocks ads and malicious domains
- **Certificate Management**: Automated SSL/TLS via Nginx Proxy Manager
- **Log Aggregation**: All containers send logs to Graylog SIEM
- **Update Monitoring**: Watchtower tracks available updates

## 📊 Monitoring

- **Service Health**: Uptime Kuma monitors all critical services
- **Log Analysis**: Graylog aggregates and analyzes container logs
- **Disk Usage**: TrueNAS built-in monitoring
- **UPS Status**: NUT (Network UPS Tools) integration

## 🎯 Use Cases

- **Digital Forensics Lab**: Secure evidence storage and analysis environment
- **Penetration Testing**: Kali Linux VM for security research
- **Media Automation**: Fully automated media library management
- **Home Cloud**: Private photo storage, password management, file sharing
- **Learning Platform**: Hands-on experience with production infrastructure

## 📝 Notes

- All sensitive data (IPs, domains, credentials) sanitized in this repository
- Configurations are templates - customize for your environment
- This documentation represents a production homelab used daily for work and personal use

## 🔗 Related Projects

- [Blog](https://dfarid479.github.io/) - Detailed homelab posts and guides
- Forensics tools and scripts (private repository)

## 📄 License

Documentation: MIT License  
Use at your own risk - this is a personal homelab environment.

---

**Last Updated**: December 2024  
**TrueNAS Version**: 24.10.0  
**Services**: 20 containers + 1 VM
