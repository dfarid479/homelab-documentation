# External Drive Sync

Automated bidirectional sync between the Samsung T7 SSD (drive `A:`) and the TrueNAS `ExternalDriveSync` share (drive `Z:`) on the Windows client machine.

## Script Location

`C:\Scripts\SyncExternalDrive.ps1`

## How It Works

1. **Checks Z: mapping** — verifies Z: is mapped to `\\100.74.206.128\ExternalDriveSync` via Tailscale. Auto-remaps if disconnected.
2. **Checks Tailscale** — tests connectivity to `100.74.206.128:445` before proceeding.
3. **Checks T7 is connected** — looks for `A:\BACKUP_DRIVE` marker file. Exits cleanly if drive not present.
4. **Phase 1 — A: → Z:** — robocopy from T7 to NAS share (skip older files, 64 threads, unbuffered I/O).
5. **Phase 2 — Z: → A:** — robocopy from NAS share back to T7 (picks up any changes made from other machines).
6. **Cleans hidden attributes** on Z:\ after sync.
7. **Windows notification** on success.

## Schedule

Windows Task Scheduler — task name: `SyncExternalDrive`

| Setting | Value |
|---|---|
| Trigger | Every 2 hours |
| Script | `C:\Scripts\SyncExternalDrive.ps1` |
| Run as | `dfaridhtu` |
| Run level | Highest available |
| Start when available | Yes (catches missed runs) |
| Execution time limit | 4 hours |

## Robocopy Flags

| Flag | Purpose |
|---|---|
| `/E` | Copy all subdirectories including empty |
| `/XO` | Skip files older than destination (skip if dest is newer) |
| `/MT:64` | 64 threads for parallel transfer |
| `/J` | Unbuffered I/O — better throughput for large files |
| `/R:2` / `/R:1` | Max 2/1 retries on failure |
| `/W:3` | 3-second wait between retries |
| `/COPY:DAT` | Copy data, attributes, timestamps |
| `/XF *.tmp` | Exclude temp files |
| `/XD $RECYCLE.BIN` | Exclude recycle bin |

## Logs

| File | Contents |
|---|---|
| `%USERPROFILE%\Documents\DriveSync.log` | High-level sync log (start, phases, result) |
| `%USERPROFILE%\Documents\robocopy-detail.log` | Full robocopy output |
| `%USERPROFILE%\Documents\DriveSync.lock` | Lock file — prevents concurrent runs |

## Manual Operations

```powershell
# Trigger sync manually
Start-ScheduledTask -TaskName "SyncExternalDrive"

# Watch live log output
Get-Content "$env:USERPROFILE\Documents\DriveSync.log" -Wait -Tail 20

# Check task status
Get-ScheduledTask -TaskName "SyncExternalDrive" | Select-Object TaskName, State

# Remap Z: manually if needed
net use Z: \\100.74.206.128\ExternalDriveSync /user:dfaridhtu * /persistent:yes
```

## Dependencies

- **Tailscale** must be running on this machine and the TrueNAS host
- **T7 SSD** must be mounted as drive `A:` with a `BACKUP_DRIVE` marker file at the root
- **Z: share** maps to TrueNAS `ExternalDriveSync` dataset via Tailscale IP `100.74.206.128`

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `Z: not mapped` in log | Network or Tailscale dropped | Script auto-remaps; if persistent, check Tailscale is running |
| `A: drive not ready` | T7 not plugged in or wrong drive letter | Plug in T7; verify it mounts as A: |
| `Tailscale not connected` | Tailscale service down | Restart Tailscale on this machine and/or TrueNAS |
| Phase exit code > 7 | Robocopy hard error | Check `robocopy-detail.log` for specific file errors |
| Lock file stuck | Previous run killed mid-sync | Lock auto-expires after 2 hours; delete manually if needed: `Remove-Item "$env:USERPROFILE\Documents\DriveSync.lock"` |
