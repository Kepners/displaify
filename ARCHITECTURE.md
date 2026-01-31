# Displaify - Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        WINDOWS PC                                │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────────┐    │
│  │ Virtual     │───▶│ Encoder      │───▶│ Network/USB     │    │
│  │ Display     │    │ (H.264/VP9)  │    │ Transport       │    │
│  │ Driver      │    └──────────────┘    └────────┬────────┘    │
│  └─────────────┘                                  │             │
│         ▲                                         │             │
│         │ Touch/Input                             │             │
│  ┌──────┴──────┐                                  │             │
│  │ Input       │◀────────────────────────────────┐│             │
│  │ Injector    │                                 ││             │
│  └─────────────┘                                 ││             │
└──────────────────────────────────────────────────┼┼─────────────┘
                                                   ││
                           WiFi / USB              ││
                                                   ▼▼
┌─────────────────────────────────────────────────────────────────┐
│                          TABLET                                  │
│  ┌─────────────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │ Network/USB     │───▶│ Decoder      │───▶│ Display       │  │
│  │ Receiver        │    │ (Hardware)   │    │ Surface       │  │
│  └─────────────────┘    └──────────────┘    └───────────────┘  │
│         │                                            │          │
│         │                                            ▼          │
│         │◀───────────────────────────────────┌─────────────┐   │
│         │            Touch Events            │ Touch       │   │
│         └────────────────────────────────────│ Handler     │   │
│                                              └─────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Components

### Windows Application

| Component | Purpose | Tech Options |
|-----------|---------|--------------|
| Virtual Display Driver | Create virtual monitor | IddCx (Indirect Display) |
| Screen Capture | Capture display output | DXGI Desktop Duplication |
| Encoder | Compress video stream | NVENC/AMF/QSV or FFmpeg |
| Transport Layer | Send to tablet | WebSocket / USB ADB |
| Input Handler | Receive touch events | Windows Input Injection |

### Tablet Application

| Component | Purpose | Tech Options |
|-----------|---------|--------------|
| Transport Layer | Receive video stream | WebSocket / USB |
| Decoder | Decompress video | MediaCodec (Android) |
| Display Surface | Render frames | SurfaceView/TextureView |
| Touch Handler | Capture & send touch | MotionEvent listener |

## Tech Stack Options

### Option A: Native (Recommended for Performance)
- **Windows**: C++ / Rust with Win32 APIs
- **Android**: Kotlin with NDK for decoding
- **Pros**: Best performance, lowest latency
- **Cons**: More complex development

### Option B: Cross-Platform
- **Windows**: Electron + Native Node addon
- **Android**: React Native or Flutter
- **Pros**: Faster development, shared code
- **Cons**: Higher latency, larger footprint

### Option C: Hybrid
- **Windows**: Rust core + WPF UI (like LayoutForge)
- **Android**: Native Kotlin
- **Pros**: Balance of performance and dev speed
- **Cons**: Two codebases for tablet

## Connection Protocols

### WiFi Mode
```
1. Discovery: mDNS broadcast on local network
2. Handshake: TCP connection, exchange capabilities
3. Streaming: UDP for video, TCP for control/input
4. Keep-alive: Heartbeat every 1s
```

### USB Mode (Android)
```
1. ADB detection: Monitor for device connection
2. Port forward: adb forward tcp:PORT tcp:PORT
3. Streaming: Same as WiFi but over localhost
4. Benefit: No firewall issues, lower latency
```

## Virtual Display Driver

Windows requires a virtual display driver to create a second monitor. Options:

1. **IddCx (Indirect Display Driver)** - Microsoft's framework
   - Requires WHQL signing for distribution
   - Most compatible approach

2. **USB Display Adapter emulation** - Pretend to be USB display
   - Complex, may have compatibility issues

3. **Existing OSS drivers** - Use/modify existing solutions
   - usbmmidd, VirtualDisplay, etc.

## Data Flow

### Video Stream (PC → Tablet)
```
Desktop Duplication API
    ↓ (raw BGRA frames)
Hardware Encoder (NVENC/AMF/QSV)
    ↓ (H.264/HEVC NAL units)
Packetizer
    ↓ (RTP-like packets)
Transport (UDP/WebSocket)
    ↓
Tablet receives
    ↓
Hardware Decoder (MediaCodec)
    ↓ (decoded frames)
Surface rendering
```

### Touch Input (Tablet → PC)
```
Touch event on tablet
    ↓ (x, y, pressure, action)
Serialize to message
    ↓
Transport (TCP/WebSocket)
    ↓
Windows receives
    ↓
Input Injection API
    ↓
Windows processes as mouse/touch
```

## Performance Considerations

| Factor | Solution |
|--------|----------|
| Latency | Hardware encode/decode, UDP transport |
| Bandwidth | Adaptive bitrate based on connection |
| Battery (tablet) | Hardware decoding, dark UI |
| CPU (PC) | GPU encode, efficient capture |

---

*Status: DRAFT - Needs tech stack decision*
*Created: 2026-01-31*
