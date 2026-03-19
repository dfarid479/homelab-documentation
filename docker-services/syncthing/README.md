# Syncthing

Continuous file synchronization between TrueNAS, Windows workstation, and iOS devices. Used to sync the Obsidian knowledge base vault across all devices over Tailscale.

## Configuration

| Property | Value |
|----------|-------|
| **Image** | `syncthing/syncthing:latest` |
| **WebUI Port** | 20810 |
| **TCP Sync Port** | 20070 |
| **UDP Sync Port** | 20070 |
| **Config Path** | `/mnt/main-storage/syncthing-config` |
| **Vault Path** | `/mnt/main-storage/obsidian-vault` |
| **Container Mount** | `/data/obsidian-vault` |
| **Installed via** | TrueNAS App Catalog |

## TrueNAS App Install Settings

Syncthing is deployed via the TrueNAS app catalog rather than a standalone docker-compose file.

| Field | Value |
|-------|-------|
| **Timezone** | America/New_York |
| **User ID** | 568 |
| **Group ID** | 568 |
| **Config Storage Type** | Host Path → `/mnt/main-storage/syncthing-config` |
| **Additional Storage (vault)** | Host Path `/mnt/main-storage/obsidian-vault` → `/data/obsidian-vault` |

## Synced Folders

| Folder ID | Purpose | TrueNAS Path | Notes |
|-----------|---------|--------------|-------|
| `obsidian-vault` | Obsidian knowledge base | `/data/obsidian-vault` | Syncs to Windows + iOS |

## Connected Devices

| Device | Client | Path |
|--------|--------|------|
| Windows workstation | SyncTrayzor x64 | `C:\Users\[user]\iCloudDrive\iCloud~md~obsidian\Obsidian Vault` |
| iPhone | iCloud (via Windows bridge) | `On My iPhone/Obsidian/Obsidian Vault` |

> **iOS Note**: iOS Syncthing (Möbius Sync) does not support direct folder path access due to sandboxing. iOS sync is handled via iCloud — Windows acts as the bridge between Syncthing and iCloud.

## Initial Setup

1. Install Syncthing from TrueNAS App Catalog
2. Create datasets before install:
   - `/mnt/main-storage/syncthing-config`
   - `/mnt/main-storage/obsidian-vault`
3. Access WebUI at `http://10.0.0.X:20810`
4. Set a GUI password immediately under **Actions → Settings → GUI**
5. Remove the default folder (points to wrong path)
6. Add folder: ID `obsidian-vault`, path `/data/obsidian-vault`
7. On each client: add TrueNAS device ID, accept folder share

## Connectivity

- Sync traffic routes over **Tailscale** (works on LAN and remotely)
- No port forwarding required
- Connection type: TCP + QUIC over Tailscale IPv6

## Security Notes

- WebUI access via Tailscale only (not exposed externally)
- GUI password required (set on first login)
- Sync encrypted in transit via TLS 1.3
- Folder type: Send & Receive on all devices

## Backup

Syncthing config stored in `/mnt/main-storage/syncthing-config`:
```bash
# Backup
tar -czf syncthing-config-backup.tar.gz /mnt/main-storage/syncthing-config

# Restore
tar -xzf syncthing-config-backup.tar.gz -C /
```

Vault data at `/mnt/main-storage/obsidian-vault` is backed up as part of the standard storage backup schedule.
