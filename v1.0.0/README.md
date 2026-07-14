# Aegis Firewall

Real-time network security suite for Windows. Packet-level monitoring, intrusion detection, and firewall enforcement with a modern Qt interface.

---

## Features

- **Real-time Packet Capture** — Kernel-level traffic inspection via WinDivert
- **Intrusion Prevention System** — Multi-detector engine (packet flood, SYN flood, ICMP flood, UDP flood, port scan)
- **Live Connection Tracking** — Per-flow state with process attribution
- **Per-Process Traffic** — Monitor bandwidth usage by individual applications
- **DNS Monitoring** — Query logging, cache inspection, and domain-level blocking
- **Blocklist Management** — Block IPs, domains, CIDR ranges, and ports
- **Windows Firewall Integration** — Create and manage WFP rules from the UI
- **Network Isolation** — One-click kill switch for all traffic
- **Custom Rules Engine** — Condition-based matching with priority ordering
- **Theme Support** — Dark, light, and system-follow themes with accent color customization
- **Keyboard Navigation** — Full F1-F12 page shortcuts and Ctrl+ commands
- **Standalone Build** — Nuitka-packaged executable, no Python runtime required

---

## Screenshots

> Screenshots go here.

| Dashboard | Statistics | IPS |
|:---:|:---:|:---:|
| ![Dashboard](docs/screenshots/dashboard.png) | ![Statistics](docs/screenshots/statistics.png) | ![IPS](docs/screenshots/ips.png) |

---

## Requirements

### Minimum
- Windows 10 x64 (build 1809+) or Windows 11
- Administrator privileges
- 2 GB RAM
- 200 MB disk space

### Build Requirements
- Python 3.10+
- Rust 1.85+ (edition 2024)
- Visual Studio 2022 Build Tools (MSVC x64)
- PyQt6

---

## Installation

### Pre-built Release

1. Download the latest release from the Releases page
2. Extract the archive
3. Run `"Aegis Firewall.exe"` as **Administrator**

### Build from Source

```powershell
# 1. Build Rust backend
cd core
cargo build --release

# 2. Install Python dependencies
pip install PyQt6

# 3. Run in development mode
cd ../console
python main.py
```

### Nuitka Standalone Build

```powershell
# From the project root
build_release.bat
```

The output will be in `dist/Aegis Firewall/`.

---

## Running

### Development Mode

```powershell
cd console
python main.py
```

The application will automatically detect the Rust debug backend at `core/target/debug/core.exe`.

### Production Mode

Run `"Aegis Firewall.exe"` from the release directory. The application expects:

- `core.exe` in the same directory
- `WinDivert.dll` and `WinDivert64.sys` at the root
- `assets/icon.ico` for the window icon
- `config/settings.json` for configuration
- `storage/` directory for blocklist and isolation data
- `runtime/` directory for ephemeral runtime data

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                  Python GUI (PyQt6)                   │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐ ┌───────────┐ │
│  │Dashboard│Statistics│  IPS  │  DNS  │  Settings  │ │
│  └──────┘ └──────┘ └──────┘ └──────┘ └───────────┘ │
│         │ sync_manager (polling + push)              │
│         │ bridge.py (subprocess IPC)                 │
├─────────┼───────────────────────────────────────────┤
│         ▼                                             │
│  ┌───────────┐  JSON files   ┌────────────────────┐ │
│  │  runtime/  │◄────────────►│   Rust Backend     │ │
│  │ *.json     │              │   (core.exe)        │ │
│  └───────────┘              │                     │ │
│                               │  ┌───────────────┐ │ │
│                               │  │ Packet Engine  │ │ │
│                               │  │ WinDivert      │ │ │
│                               │  │ IPS Detectors  │ │ │
│                               │  │ Connection Tbl │ │ │
│                               │  │ Event Bus      │ │ │
│                               │  │ Blocklist Mgr  │ │ │
│                               │  │ Firewall API   │ │ │
│                               │  └───────────────┘ │ │
│                               └────────────────────┘ │
└─────────────────────────────────────────────────────┘
```

---

## Folder Structure

```
Aegis Firewall/
├── console/                 # Python frontend
│   ├── main.py              # Entry point
│   ├── window.py            # Main window, sidebar, shortcuts
│   ├── sync_manager.py      # Data synchronization layer
│   ├── bridge.py            # Rust backend IPC bridge
│   ├── toast.py             # Notification toast widget
│   ├── theme.py             # Theme definitions
│   ├── theme_manager.py     # Theme management
│   ├── settings_manager.py  # Settings persistence
│   ├── event_client.py      # Push event client
│   ├── empty_state.py       # Empty state placeholder
│   ├── spinner.py           # Loading spinner
│   ├── status_badge.py      # Status indicator widget
│   ├── assets/              # Icons and resources
│   └── pages/               # UI page modules
│       ├── new_dashboard.py
│       ├── connections.py
│       ├── blocklist.py
│       ├── isolation.py
│       ├── events.py
│       ├── statistics.py
│       ├── dns.py
│       ├── processes.py
│       ├── ips.py
│       ├── rules.py
│       ├── settings.py
│       ├── logs.py
│       └── about.py
├── core/                    # Rust backend
│   ├── Cargo.toml
│   └── src/
│       ├── main.rs          # Entry point, CLI arg parsing
│       ├── lib.rs           # Library root
│       ├── domain/          # Domain models
│       ├── modules/         # Feature modules
│       │   ├── packet/      # Packet engine, detectors, pipeline
│       │   ├── connection/  # Connection tracking
│       │   ├── dns/         # DNS monitoring
│       │   ├── blocklist/   # Blocklist management
│       │   ├── isolation/   # Network isolation
│       │   ├── socket/      # Socket monitoring
│       │   ├── event_bus/   # Event dispatch
│       │   └── windows/     # Windows API integration
│       ├── services/        # Service layer
│       ├── shared/          # Shared utilities
│       ├── platform/        # Platform-specific code
│       └── traits/          # Trait definitions
├── docs/                    # Documentation
├── build/                   # Build artifacts
├── dist/                    # Release output
├── build_release.bat        # Nuitka release builder
├── LICENSE
├── NOTICE.md
├── README.md
├── CHANGELOG.md
├── SECURITY.md
├── CONTRIBUTING.md
└── CODE_OF_CONDUCT.md
```

---

## Technology Stack

| Component | Technology |
|-----------|------------|
| Frontend GUI | Python 3, PyQt6 |
| Backend Engine | Rust (edition 2024) |
| Packet Capture | WinDivert (kernel driver) |
| IPC | JSON files + subprocess |
| Serialization | serde / serde_json |
| Connection Tracking | dashmap (concurrent hashmap) |
| Event Bus | crossbeam-channel |
| Windows API | windows / windows-sys crates |
| Packaging | Nuitka (standalone EXE) |

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| F1 | Dashboard |
| F2 | Connections |
| F3 | Blocklist |
| F4 | Isolation |
| F5 | Events |
| F6 | Statistics |
| F7 | DNS |
| F8 | Processes |
| F9 | IPS |
| F10 | Rules |
| F11 | Toggle Fullscreen |
| F12 | About |
| Ctrl+R | Refresh current page |
| Ctrl+Shift+R | Force sync firewall |
| Ctrl+D | Cycle theme |
| Ctrl+Q | Quit |
| Ctrl+F | Focus search bar |
| Ctrl+L | Clear search |
| Esc | Close dialog / hide toast |

---

## Security Notice

Aegis Firewall requires **Administrator privileges** to function. The application:

- Captures and inspects all network packets passing through the system
- Can block or redirect network traffic
- Modifies Windows Firewall rules
- Reads process and socket information system-wide

All packet processing is performed **locally**. No data is transmitted externally. No telemetry, analytics, or usage data is collected. See [SECURITY.md](SECURITY.md) for details.

---

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

Copyright (c) 2026 Kanishk

---

## Credits

- **WinDivert** — Packet capture and injection library
- **PyQt6** — Cross-platform GUI framework
- **Rust** — Safe and performant systems programming
- **Contributor Covenant** — Code of Conduct template
