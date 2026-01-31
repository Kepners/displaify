# Displaify - Technical Specification

## Overview
**Displaify** turns your Android tablet into a wireless or USB second display for Windows. Based on [Weylus](https://github.com/H-M-H/Weylus) with improvements for Windows support, virtual display creation, and freemium licensing.

## Core Decision: Fork Weylus
| Why Weylus | Benefit |
|------------|---------|
| Rust-based | Fast, safe, matches LayoutForge skills |
| USB via ADB | Low-latency connection (35-70ms) |
| Touch + stylus | Essential feature built-in |
| Hardware encoding | NVENC/MediaFoundation support |
| ~50MB footprint | No Electron bloat |
| AGPL-3.0 license | Can fork and modify |

## Goals
- [x] Fork Weylus as foundation
- [ ] Improve Windows support (currently "limited")
- [ ] Add IddCx virtual display driver integration
- [ ] Add freemium licensing (720p free, full-res pro)
- [ ] Optimize for Lenovo tablets
- [ ] Create polished installer/UX

## User Stories
- As a user, I want to extend my Windows display to my tablet via USB for low latency
- As a user, I want to connect via WiFi when USB isn't convenient
- As a user, I want to touch my tablet and have it control Windows
- As a user, I want the free version to work at 720p
- As a user, I want to purchase a license key for full resolution

## Target Platforms

### Windows App (Host)
- Windows 10 (1903+)
- Windows 11
- x64 architecture
- Virtual display via IddCx driver

### Tablet App (Client)
- Android 8.0+ (API 26+)
- Any Android tablet
- Special testing: Lenovo tablets
- Web browser-based client (like Weylus)

## Technical Architecture

### Based on Weylus
```
┌─────────────────────────────────────────────────────────────┐
│                    WINDOWS PC (Displaify)                   │
│  ┌─────────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │ Virtual     │───▶│ Screen       │───▶│ H.264 Encoder │  │
│  │ Display     │    │ Capture      │    │ (NVENC/MF)    │  │
│  │ (IddCx)     │    │ (Win32 API)  │    └───────┬───────┘  │
│  └─────────────┘    └──────────────┘            │          │
│         ▲                                        │          │
│         │ Touch Events                           │          │
│  ┌──────┴──────┐                                │          │
│  │ Input       │◀────────────────────────┐      │          │
│  │ Injection   │                         │      │          │
│  └─────────────┘                         │      │          │
│                                          │      │          │
│  ┌─────────────────────────────────────┐ │      │          │
│  │ WebSocket Server (Rust)             │◀┴──────┘          │
│  └─────────────────────────────────────┘                   │
└──────────────────────────────────────────────────┬─────────┘
                                                   │
                        USB (ADB) / WiFi           │
                                                   ▼
┌─────────────────────────────────────────────────────────────┐
│                    ANDROID TABLET                           │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Web Browser (Chrome/Firefox)                        │   │
│  │ ┌─────────────┐  ┌──────────────┐  ┌────────────┐  │   │
│  │ │ H.264       │  │ Canvas       │  │ Touch      │  │   │
│  │ │ Decoder     │──│ Renderer     │  │ Capture    │──┼───│
│  │ │ (Hardware)  │  │              │  │            │  │   │
│  │ └─────────────┘  └──────────────┘  └────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Weylus Improvements Needed
| Area | Current (Weylus) | Displaify Target |
|------|------------------|------------------|
| Windows capture | Limited | Full (DXGI Desktop Duplication) |
| Virtual display | None | IddCx driver integration |
| Licensing | None | License key system |
| Resolution limit | None | 720p free / full pro |
| UI/UX | Developer-focused | Consumer-friendly |
| Installer | Manual | MSIX/Installer wizard |

## Connection Methods

### 1. USB Connection (Primary)
```bash
# User connects tablet via USB
# Enable USB debugging on tablet
# Displaify auto-detects via ADB
adb forward tcp:1701 tcp:1701
# WebSocket over localhost = ~35-70ms latency
```

### 2. WiFi Connection (Secondary)
```
# Discovery via mDNS/Bonjour
# Direct WebSocket connection
# Latency: 50-150ms depending on network
```

## Freemium Model

### Free Tier
- 720p maximum resolution
- USB + WiFi connections
- Touch input
- Watermark: "Displaify Free" corner badge

### Pro Tier ($19.99 one-time)
- Full native resolution (up to 4K)
- No watermark
- Priority support
- License key system (like LayoutForge)

### License Implementation
```rust
// Check license on startup
fn check_license() -> LicenseStatus {
    // 1. Check local license file
    // 2. Validate against stored key
    // 3. Return Free or Pro status
}

// Enforce resolution limit
fn get_max_resolution(license: LicenseStatus) -> (u32, u32) {
    match license {
        LicenseStatus::Free => (1280, 720),
        LicenseStatus::Pro => (3840, 2160),
    }
}
```

## MVP Features (v1.0)
1. ✅ Fork Weylus codebase
2. Improve Windows screen capture (DXGI)
3. USB connection via ADB (auto-detect)
4. WiFi connection (manual IP or QR code)
5. Touch input passthrough
6. 720p resolution (free tier)
7. Basic UI with connection status
8. License key validation

## v1.1 Features
- IddCx virtual display driver
- Full resolution (pro tier)
- Installer wizard
- Auto-update system

## v2.0 Features
- Stylus/pen support with pressure
- Multiple tablet support
- iOS support (stretch goal)
- Audio routing

## Performance Targets
| Metric | USB | WiFi |
|--------|-----|------|
| Latency | <50ms | <100ms |
| Frame rate | 60fps | 30-60fps |
| Resolution | Up to 4K | Up to 1080p |
| CPU usage | <10% | <15% |

## Development Phases

### Phase 1: Fork & Understand (Week 1-2)
- Clone Weylus repository
- Build and run locally
- Document Windows limitations
- Identify improvement areas

### Phase 2: Windows Improvements (Week 3-4)
- Implement DXGI Desktop Duplication
- Fix Windows-specific bugs
- Test on Windows 10/11

### Phase 3: Licensing & Polish (Week 5-6)
- Add license key system
- Implement resolution limits
- Create branded UI
- Build installer

### Phase 4: Testing & Launch (Week 7-8)
- Test on multiple Android tablets
- Lenovo-specific testing
- Beta release
- Gather feedback

## Competitors Reference
| App | Price | Latency | USB | Touch |
|-----|-------|---------|-----|-------|
| Duet Display | $10-25 | ~30ms USB | ✅ | ✅ |
| Spacedesk | Free | ~100ms | ❌ | ✅ |
| SuperDisplay | $10 | ~20ms | ✅ | ✅ |
| **Displaify** | Free/Pro | ~40ms | ✅ | ✅ |

## Design System
| Color | Hex | Usage |
|-------|-----|-------|
| Primary | `#5B8E7D` | Headers, buttons |
| Secondary | `#F4A259` | Accents, status |
| Error | `#BC4B51` | Disconnected |
| Success | `#8CB369` | Connected |
| Warning | `#F4E285` | Connecting |

## Repository Structure
```
displaify/
├── src/                    # Rust source (forked from Weylus)
│   ├── capture/            # Screen capture (DXGI)
│   ├── encode/             # H.264 encoding
│   ├── input/              # Touch injection
│   ├── license/            # License validation
│   └── main.rs
├── web/                    # TypeScript web client
│   ├── src/
│   └── index.html
├── driver/                 # IddCx virtual display (future)
├── installer/              # MSIX/WiX installer
├── docs/
│   └── SPEC.md             # This file
├── CLAUDE.md
├── ARCHITECTURE.md
└── README.md
```

---

## Sources & References
- [Weylus GitHub](https://github.com/H-M-H/Weylus) - Base project
- [Virtual-Display-Driver](https://github.com/VirtualDrivers/Virtual-Display-Driver) - IddCx reference
- [scrcpy](https://github.com/Genymobile/scrcpy) - ADB/latency reference
- [Deskreen](https://github.com/pavlobu/deskreen) - WebRTC alternative

---

*Status: APPROVED - Ready for Phase 1*
*Created: 2026-01-31*
*Based on: /sc:brainstorm requirements discovery session*
