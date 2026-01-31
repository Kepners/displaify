# Displaify - Specification

## Overview
**Displaify** turns your tablet into a wireless or wired second display for Windows. Works via WiFi for convenience or USB for low-latency performance. Includes support for Lenovo tablets.

## Goals
- [ ] Extend Windows display to tablet seamlessly
- [ ] Support both WiFi and USB connections
- [ ] Low latency suitable for productivity work
- [ ] Easy setup (minimal configuration)
- [ ] Support Lenovo tablets specifically

## User Stories
- As a user, I want to extend my Windows display to my tablet so I can have more screen space
- As a user, I want to connect via USB for lower latency when doing precision work
- As a user, I want to connect via WiFi for convenience when I don't have a cable
- As a user, I want my tablet to auto-reconnect if the connection drops
- As a user, I want to adjust resolution and orientation on my tablet display

## Target Platforms

### Windows App
- Windows 10 (1903+)
- Windows 11
- x64 architecture

### Tablet App
- Android 8.0+ (API 26+)
- iOS 13+ (if supported)
- Special focus: Lenovo tablets

## Technical Requirements

### Connection Methods
1. **WiFi Connection**
   - Discovery via mDNS/Bonjour
   - Streaming protocol: TBD (H.264/VP9)
   - Port: TBD

2. **USB Connection**
   - ADB-based connection (Android)
   - Lower latency than WiFi
   - Auto-detect when tablet plugged in

### Display Features
- Extend mode (second monitor)
- Mirror mode (duplicate primary)
- Custom resolution support
- Portrait/Landscape orientation
- Touch input passthrough (tablet → PC)

## MVP Features
1. WiFi display extension (basic streaming)
2. USB display extension (ADB tunnel)
3. Resolution/orientation settings
4. Connection status indicator
5. Auto-reconnect on disconnect

## Future Features
- Touch gesture support (multi-touch)
- Pen/stylus support
- Audio routing to tablet
- iOS support
- Multiple tablet support
- Retina/HiDPI support

## Performance Targets
| Metric | WiFi | USB |
|--------|------|-----|
| Latency | <50ms | <20ms |
| Frame rate | 30fps | 60fps |
| Resolution | Up to 1080p | Up to native |

## Competitors / Research
- Duet Display
- Splashtop Wired XDisplay
- SpaceDesk
- SuperDisplay

---

*Status: DRAFT - Needs tech stack decision*
