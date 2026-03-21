# Syncthing

Continuous file synchronization between TrueNAS and Windows workstation. Syncs the Obsidian knowledge base vault at the filesystem level over Tailscale.

> **Note**: iOS sync is handled by **CouchDB + Obsidian LiveSync** (see [couchdb/README.md](../couchdb/README.md)), not Syncthing. Syncthing handles TrueNAS ↔ Windows file-level sync only.

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

| Folder ID | Purpose | TrueNAS Path | Devices |
|-----------|---------|--------------|---------|
| `obsidian-vault` | Obsidian knowledge base files | `/data/obsidian-vault` | TrueNAS + Windows |

## Connected Devices

| Device | Client | Path |
|--------|--------|------|
| TrueNAS | Syncthing (App Catalog) | `/data/obsidian-vault` |
| Windows workstation | SyncTrayzor x64 | `C:\Users\dfaridhtu\Documents\Obsidian Vault` |

**iPhone** — handled by CouchDB/LiveSync, not Syncthing. See [../couchdb/README.md](../couchdb/README.md).

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

## Relationship to CouchDB/LiveSync

| | Syncthing | CouchDB + LiveSync |
|---|---|---|
| **Devices** | TrueNAS ↔ Windows | Windows ↔ iPhone |
| **Protocol** | File sync (block-level delta) | CouchDB replication (document-level) |
| **iOS support** | No | Yes |
| **Real-time** | Yes (on change) | Yes (on change) |
| **Encryption** | TLS in transit | TLS in transit + E2EE at rest |
| **Role** | Filesystem backup + Windows sync | Active cross-device sync |

The two coexist without conflict — LiveSync does not touch the filesystem path that Syncthing manages.
