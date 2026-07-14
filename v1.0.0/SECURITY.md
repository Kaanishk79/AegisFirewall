# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability in Aegis Firewall, please report it privately by emailing [INSERT EMAIL]. Do not disclose the issue publicly until it has been addressed.

We will acknowledge receipt within 48 hours and provide a timeline for a fix. Security issues are our highest priority and will be addressed before any other work.

## Security Model

### Administrator Requirement

Aegis Firewall requires **Administrator privileges** to run. This is necessary because:

1. **WinDivert Driver**: The kernel-level packet filter driver must be installed and loaded, which requires administrative rights.
2. **Windows Firewall Integration**: Modifying Windows Filtering Platform (WFP) rules via `netsh advfirewall` requires elevation.
3. **Hosts File Modification**: Blocking domains via the system hosts file requires write access to `C:\Windows\System32\drivers\etc\hosts`.
4. **Process Enumeration**: Querying detailed process information across all running processes requires elevated access.

### Driver Permissions

The WinDivert driver (`WinDivert64.sys`) operates at the kernel level and has access to all network traffic passing through the system. The driver is:

- Digitally signed (required by 64-bit Windows)
- Loaded only when monitoring is active
- Unloaded when monitoring stops
- Restricted to the Windows Packet Filter (WinDivert) API, which provides read and selective block capabilities

### Privacy

Aegis Firewall processes network traffic locally on the machine. No data is sent to external servers. Specifically:

- Packet data is examined in-memory and never transmitted
- DNS queries are logged locally for visibility
- Statistics are stored in local JSON files
- No telemetry, analytics, or usage data is collected
- No network requests are made by the application itself (except for DNS resolution needed to display DNS entries)

### Data Storage

Runtime data is stored in the `runtime/` directory as JSON files:

- `connections.json` — Active connection records (cleared on restart)
- `packet_stats.json` — Cumulative packet statistics
- `packet_events.json` — Event log entries
- `dns_stats.json` — DNS query statistics
- `dns_cache.json` — DNS resolution cache

These files contain network metadata (IP addresses, ports, process names) but no payload content. They are stored locally and never transmitted.

## Best Practices

1. Always run the latest version
2. Run only on trusted networks when using blocking features
3. Review blocklist entries periodically
4. Understand that no firewall provides 100% protection
5. Use in conjunction with antivirus and other security tools
