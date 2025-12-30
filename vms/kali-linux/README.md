# Kali Linux VM

Penetration testing and security research virtual machine.

## Specifications

| Property | Value |
|----------|-------|
| **RAM** | 8GB |
| **vCPUs** | 4 |
| **Disk** | 100GB |
| **Network** | Bridged to homelab network |

## Purpose

- Digital forensics analysis
- Penetration testing practice
- Security tool development
- Bug bounty research
- CTF competitions

## Installed Tools

- Burp Suite
- Metasploit Framework
- Nmap
- Wireshark
- John the Ripper
- Hashcat
- SQLMap
- Aircrack-ng
- And 300+ other security tools

## Network Access

- Internal network: 10.0.0.0/24
- Tailscale VPN access for remote work
- Isolated from production services

## Snapshots

Regular snapshots before major testing:
- Pre-testing baseline
- After tool updates
- Known-good configurations

## Security

- Disabled unnecessary services
- Firewall configured
- No internet-facing services
- Used only for ethical testing

## Future Plans

- SDR integration with Raspberry Pi 5
- Wireless security testing
- Custom tool development
