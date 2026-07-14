# Changelog

All notable changes to Aegis Firewall are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2026-07-12

### Added

#### Rust Backend
- Real-time packet capture engine using WinDivert
- Multi-threaded packet processing pipeline
- Intrusion Prevention System (IPS) with configurable detectors:
  - Packet flood detector
  - SYN flood detector
  - ICMP flood detector
  - UDP flood detector
  - Port scan detector
- Connection tracking table with per-flow state
- DNS monitoring and cache
- Process-to-socket mapping via Windows IPHLPAPI
- Blocklist management with persistent storage
- Windows Firewall integration (netsh advfirewall)
- Hosts file-based domain blocking
- Network isolation (inbound/outbound block via WFP)
- JSON-based IPC via runtime files
- Event bus with crossbeam channels
- Push event subscription model
- Packet statistics aggregation

#### Python GUI
- Dashboard with real-time monitoring overview
- Live statistics with capture rate, packet/thread/block counts
- Connection table with process metadata, threat scoring
- DNS query log and cache viewer
- Process tree with per-process traffic breakdown
- IPS findings table with detail drill-down
- Events log with severity filtering
- Blocklist management (IP, domain, CIDR, port rules)
- Windows Firewall rule management
- Network isolation toggle
- Rules engine with condition-based matching
- Appearance settings (dark/light/system theme, accent color)
- Sync interval configuration
- Keyboard shortcuts (F1-F12 navigation, Ctrl+R refresh, etc.)
- Toast notification system
- About page with features overview

#### Packaging
- Nuitka standalone build for Windows
- Single-file executable deployment
- Release directory assembly script (`build_release.bat`)
- WinDivert DLL and SYS bundling

#### Documentation
- User guide with page-by-page walkthrough
- Developer guide with architecture deep-dive
- Architecture diagrams (ASCII)
- Packet pipeline documentation
- Runtime file format documentation
- Event system documentation
- Security policy
- Contributing guidelines
- Code of Conduct

### Changed
- Renamed application from MyFirewall to Aegis Firewall
- Restored IPS detection thresholds to production defaults
- Removed diagnostic/testing instrumentation

### Fixed
- Packet detection thresholds consistency
- IPS event_id tracking for stable row expansion
- Connection and process data synchronization
- Blocklist rule enable/disable persistence

[1.0.0]: https://github.com/kanishk/Aegis/releases/tag/v1.0.0
