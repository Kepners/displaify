# Displaify

**Turn your tablet into a second display for Windows** - connect via WiFi or USB.

Based on [Weylus](https://github.com/H-M-H/Weylus) with improvements for Windows, virtual display support, and freemium licensing.

## Features

- **Extend your display** - Use your tablet as a true second monitor
- **WiFi or USB** - Wireless convenience or wired low-latency (~35-70ms)
- **Touch passthrough** - Interact with Windows from your tablet
- **Pen/stylus support** - Pressure and tilt sensitivity
- **Hardware acceleration** - NVENC and MediaFoundation encoding

## Status

🚧 **In Development** - Rebranding from Weylus in progress.

## Supported Platforms

### Host (Windows PC)
- Windows 10 (1903+)
- Windows 11
- x64 architecture

### Client (Tablet)
- Any modern web browser
- Android 8.0+ tablets
- iOS/iPadOS 13+ (Safari or Firefox)

## Running

1. Start Displaify on your Windows PC
2. Note the URL displayed (e.g., `http://192.168.1.100:1701`)
3. Open that URL in your tablet's browser
4. Start using your tablet as a second screen!

### USB Connection (Lower Latency)

For Android devices, you can use USB for better latency:

```bash
# Enable USB debugging on your tablet
# Connect via USB
adb reverse tcp:1701 tcp:1701
adb reverse tcp:9001 tcp:9001
# Open http://127.0.0.1:1701 on your tablet
```

## Building

### Prerequisites
- Rust (latest stable)
- Visual Studio Build Tools (for Windows)
- TypeScript

### Build
```bash
cargo build --release
```

The binary will be at `target/release/displaify.exe`

## Configuration

Config file location: `%APPDATA%/displaify/displaify.toml`

### Environment Variables
- `DISPLAIFY_LOG_LEVEL` - Set log level (TRACE, DEBUG, INFO, WARN, ERROR)
- `DISPLAIFY_LOG_JSON` - Enable JSON logging format

## Documentation

- [Specification](docs/SPEC.md) - Project goals and requirements
- [Architecture](ARCHITECTURE.md) - Technical design

## Credits

Displaify is based on [Weylus](https://github.com/H-M-H/Weylus) by HMH.

## License

AGPL-3.0-or-later

---

## Brand Colors

| Color | Hex | Name |
|-------|-----|------|
| ![#5B8E7D](https://via.placeholder.com/15/5B8E7D/5B8E7D.png) | `#5B8E7D` | Jungle Teal |
| ![#F4A259](https://via.placeholder.com/15/F4A259/F4A259.png) | `#F4A259` | Sandy Brown |
| ![#BC4B51](https://via.placeholder.com/15/BC4B51/BC4B51.png) | `#BC4B51` | Blushed Brick |
| ![#F4E285](https://via.placeholder.com/15/F4E285/F4E285.png) | `#F4E285` | Light Gold |
| ![#8CB369](https://via.placeholder.com/15/8CB369/8CB369.png) | `#8CB369` | Muted Olive |
