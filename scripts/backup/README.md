# Backup Scripts

Automated backup procedures for homelab services.

## homelab-backup.sh

Production-ready backup script for Docker container volumes.

### Features

- Stops containers for consistency
- Creates compressed archives
- Implements retention policies
- Email notifications
- Graylog logging integration
- Checksum verification

### Usage

```bash
./homelab-backup.sh
```

### Schedule

Run via cron:
```
0 2 * * * /path/to/homelab-backup.sh
```

## Backup Locations

- Local: `/mnt/main-storage/backups/`
- External: Manually copied to external HDD monthly

## Retention

- Daily backups: 30 days
- Weekly backups: 60 days  
- Monthly backups: 180 days
