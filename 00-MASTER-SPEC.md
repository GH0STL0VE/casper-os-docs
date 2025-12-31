# GH0STL0VE Master Specification
## CASPER Operating System & HEART Hub Ecosystem

**Version:** 1.0  
**Last Updated:** December 30, 2025  
**Status:** 🚧 In Development - Phone arriving in 1-2 weeks

> **Ghost** = CASPER (Portable AI Assistant)  
> **Love** = HEART (Home Hub)  
> **Home is where the HEART is** 🖤

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Architecture](#system-architecture)
3. [Hardware Specifications](#hardware-specifications)
4. [CASPER-OS (Custom ROM)](#casper-os-custom-rom)
5. [CASPER Device Architecture](#casper-device-architecture)
6. [HEART Hub Architecture](#heart-hub-architecture)
7. [Communication Contract](#communication-contract)
8. [Tool Ecosystem](#tool-ecosystem)
9. [Port Allocation](#port-allocation)
10. [Tech Stack](#tech-stack)
11. [Development Roadmap](#development-roadmap)
12. [Repository Structure](#repository-structure)

---

## Executive Summary

**GH0STL0VE** is a dual-component AI assistant ecosystem designed to externalize executive function for someone with ADHD:

- **CASPER** (Coordinated Assistance System for Personal Executive Reasoning)
  - Portable device running custom Android ROM
  - Voice-first interface for immediate assistance
  - Offline-capable with local processing
  - Syncs with HEART when on WiFi

- **HEART** (Household Executive Autonomous Research Tool)
  - MacBook-based hub with powerful reasoning capabilities
  - Orchestrates specialized tools (HANK, Grocery, etc.)
  - Provides web dashboard for monitoring and management
  - Long-term memory and data storage

**Core Philosophy:**
- **Delegate, don't manage** - Tools own their domains completely
- **Radical ownership** - Each tool is responsible for its area
- **Anticipatory behavior** - Proactive assistance before asking
- **Loud notifications** - Can't be ignored (designed for ADHD)

---

## System Architecture

```
┌───────────────────────────────────────────────────────────────────┐
│                        GH0STL0VE ECOSYSTEM                        │
│                                                                   │
│  ┌─────────────────────┐           ┌──────────────────────────┐  │
│  │      CASPER         │           │         HEART            │  │
│  │   (OnePlus 8T)      │◄─────────►│      (MacBook Pro)       │  │
│  │                     │  WebSocket│                          │  │
│  │  ┌──────────────┐   │           │  ┌────────────────────┐  │  │
│  │  │ Custom ROM   │   │           │  │  Tool Orchestrator │  │  │
│  │  │ (LineageOS)  │   │           │  ├────────────────────┤  │  │
│  │  ├──────────────┤   │           │  │ HANK (Port 5100)   │  │  │
│  │  │ CASPERLauncher│  │           │  │ Grocery (5200)     │  │  │
│  │  │ CASPERServices│  │           │  │ Future Tools...    │  │  │
│  │  ├──────────────┤   │           │  ├────────────────────┤  │  │
│  │  │ casper-core  │   │           │  │  heart-core (5000) │  │  │
│  │  │ daemon       │   │           │  │  Web Dashboard     │  │  │
│  │  └──────────────┘   │           │  │  iMessage Bridge   │  │  │
│  │                     │           │  └────────────────────┘  │  │
│  │  • Voice I/O        │           │  • Claude API          │  │
│  │  • Touchscreen UI   │           │  • Complex reasoning   │  │
│  │  • Local LLM        │           │  • Data persistence    │  │
│  │  • Offline capable  │           │  • Proactive engine    │  │
│  └─────────────────────┘           └──────────────────────────┘  │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

---

## Hardware Specifications

### CASPER Device

**Model:** OnePlus 8T (`kebab`)
- **SoC:** Qualcomm Snapdragon 865
- **Display:** 6.55" AMOLED, 120Hz, 1080×2400
- **RAM:** 8GB / 12GB
- **Storage:** 128GB / 256GB UFS 3.1
- **Battery:** 4500mAh with Warp Charge 65
- **Connectivity:** WiFi 6, Bluetooth 5.1, NFC
- **Bootloader:** Unlockable (official OnePlus support)
- **ROM Support:** Official LineageOS 21 (Android 14)

**Why OnePlus 8T:**
- Strong custom ROM community
- Unlockable bootloader
- Excellent hardware specs
- Good battery life
- Active LineageOS development
- Affordable ($300-400 used)

### HEART Hub

**Model:** MacBook Pro (M-series or Intel)
- **OS:** macOS (current)
- **Role:** Always-on home server
- **Services:** Python daemons via launchd
- **Storage:** Local SQLite databases
- **Network:** Home WiFi hub

---

## CASPER-OS (Custom ROM)

### Base System

**Foundation:** LineageOS 21 fork (Android 14)

**Repository Structure:**
```
casper-os-build/
├── manifest/                    # Repo manifest
├── device/oneplus/kebab/        # Device configs
├── vendor/casper/               # GH0STL0VE customizations
│   ├── overlay/                 # UI customizations
│   ├── apps/                    # Pre-installed apps
│   │   ├── CASPERLauncher/     # Custom launcher
│   │   ├── CASPERCore/         # Frontend app
│   │   └── CASPERServices/     # System services
│   ├── sepolicy/               # Security policies
│   └── config/                 # Build configs
├── kernel/oneplus/sm8250/      # Kernel
└── system/                     # Core Android
```

### Customizations

**Removed:**
- All Google services and apps
- Default launchers (Trebuchet, etc.)
- Unnecessary system apps
- Play Store access (intentional)

**Added:**
- CASPERLauncher (mandatory, cannot be changed)
- CASPERServices (system daemon)
- Termux (pre-installed, system app)
- F-Droid (for updates only)

**Modified:**
- Boot animation (CASPER branding)
- System properties (device name, etc.)
- Notification behavior (louder, more persistent)
- Power management (optimized for all-day use)
- Lock screen (voice activation default)

### Build Configuration

**vendor/casper/casper.mk:**
```makefile
PRODUCT_NAME := casper_kebab
PRODUCT_DEVICE := kebab
PRODUCT_BRAND := CASPER
PRODUCT_MODEL := CASPER-OS
PRODUCT_MANUFACTURER := GH0STL0VE

# Remove unwanted
PRODUCT_PACKAGES += \
    -Browser \
    -Calendar \
    -Camera2 \
    -Gallery2

# Add CASPER apps
PRODUCT_PACKAGES += \
    CASPERLauncher \
    CASPERServices \
    CASPERCore \
    Termux

# Properties
PRODUCT_PROPERTY_OVERRIDES += \
    ro.casper.version=1.0 \
    ro.setupwizard.mode=DISABLED \
    persist.sys.force_launcher=com.casper.launcher
```

---

## CASPER Device Architecture

### System Services (CASPERServices)

**Service Stack:**
```kotlin
// CASPERServices.apk - System-level daemon
CASPERCoreService
├── HTTP/WebSocket Server (port 5200)
├── Communication Contract Client
├── Voice Processing
│   ├── Wake word detection
│   ├── Speech-to-text (local Whisper)
│   └── Text-to-speech synthesis
├── Tool Registry
├── HEART Connection Manager
└── Notification Handler
```

**Auto-start on boot via Android init.d**

### Application Layer

#### CASPERLauncher (System Default)

**Purpose:** Single-purpose interface, cannot be changed

**UI Layout:**
```
┌─────────────────────────────────────┐
│ CASPER          ⚡ HEART   🔋 87%   │ ← Status bar
├─────────────────────────────────────┤
│                                     │
│   🎤 Voice Input                    │
│                                     │
│   ┌─────┐ ┌─────┐ ┌─────┐          │
│   │HANK │ │ STT │ │Task │          │ ← Tool grid
│   └─────┘ └─────┘ └─────┘          │
│                                     │
│   ┌─────┐ ┌─────┐ ┌─────┐          │
│   │Note │ │Timer│ │ ... │          │
│   └─────┘ └─────┘ └─────┘          │
│                                     │
│   "Hey CASPER..."                   │ ← Voice prompt
└─────────────────────────────────────┘
```

**Features:**
- Full-screen tool grid
- Voice activation button
- HEART sync status indicator
- Battery/system status
- No app drawer (locked down)
- Gesture controls

**Gestures:**
- Swipe up: Voice input
- Swipe down: Quick settings/notifications
- Tap tool: Launch
- Long-press tool: Configure
- 3-finger swipe: HEART sync status

#### CASPERCore (Frontend App)

**Built with:** Flutter or React Native

**Capabilities:**
- Native touchscreen UI
- WebSocket client to CASPERServices
- Tool launcher and management
- Voice interaction UI
- Status monitoring
- Settings (minimal)

### Backend Services (Termux)

**Bundled as system app, runs:**
```bash
# Python environment
casper-core daemon (port 5200)
├── FastAPI HTTP server
├── WebSocket server
├── Communication Contract implementation
├── Local LLM integration (optional)
├── Voice processing pipeline
└── HEART sync client
```

**Why Termux:**
- Full Python/Linux environment
- Can reuse backend code across platforms
- Easy package management
- Background execution support

### Offline Capabilities

**Without HEART connection:**
- Voice input still works
- Local speech recognition
- Basic tool operations
- Cached responses
- Queue actions for sync

**When WiFi available:**
- Auto-connect to HEART
- Sync queued actions
- Update tool states
- Receive notifications

---

## HEART Hub Architecture

### Core Services

**heart-core (Port 5000)**
```python
# Main orchestration daemon
heart_core/
├── __init__.py
├── daemon.py              # Main service
├── tool_registry.py       # Tool management
├── message_router.py      # Route messages between tools
├── casper_gateway.py      # CASPER connection handler
├── reasoning_engine.py    # Claude API integration
├── proactive_engine.py    # Scheduled/active behaviors
└── imessage_bridge.py     # iMessage integration (future)
```

**Responsibilities:**
- Tool registration and discovery
- Message routing between tools
- CASPER WebSocket server
- Claude API reasoning
- Proactive scheduling
- Data persistence

### Tool Orchestration

**Tools run independently:**
- Each tool is a separate process
- Owns its domain completely
- Registers with HEART on startup
- Exposes Communication Contract endpoints
- Operates autonomously

**HEART's role:**
- Maintain tool registry
- Route messages when needed
- Coordinate cross-tool actions
- Provide shared infrastructure (Claude API, iMessage)
- Monitor health

### Web Dashboard

**heart-dashboard (Port 5001)**
```
React/Vue Web App
├── Tool Status Grid
├── CASPER Connection Monitor
├── System Logs Viewer
├── Tool Configuration
├── Data Visualizations
└── Health Monitoring
```

**Access:** http://localhost:5001 on MacBook

### Deployment (macOS)

**launchd services:**
```xml
<!-- ~/Library/LaunchAgents/com.ghostlove.heart-core.plist -->
<plist>
  <dict>
    <key>Label</key>
    <string>com.ghostlove.heart-core</string>
    <key>ProgramArguments</key>
    <array>
      <string>/path/to/venv/bin/python</string>
      <string>/path/to/heart/heart-core.py</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
  </dict>
</plist>
```

**Prevent sleep:** `caffeinate` or System Settings

---

## Communication Contract

### Core Principles

1. **Tools own their domains** - Complete autonomy
2. **HTTP + WebSocket** - Standard protocols
3. **JSON payloads** - Simple, universal
4. **No shared state** - Message passing only
5. **Health checks** - All tools must respond to `/health`

### Required Endpoints

**Every tool MUST implement:**

```
POST /api/tools/register
  → Register with HEART on startup
  → Payload: {tool_id, name, port, capabilities, icon}
  → Response: {status, heart_url}

GET /casper/state
  → Expose current state for CASPER display
  → Response: {tool_id, current_state, quick_actions}

POST /casper/command
  → Accept commands from CASPER/HEART
  → Payload: {command, params, source}
  → Response: {status, result, next_actions}

GET /health
  → Health check endpoint
  → Response: {status: "healthy", uptime, version}
```

### Message Types

#### Tool Registration
```json
{
  "type": "tool_register",
  "tool_id": "hank-chores",
  "name": "HANK Chore Manager",
  "port": 5100,
  "capabilities": ["voice_input", "notifications", "scheduling"],
  "icon": "🧹",
  "version": "1.0.0"
}
```

#### Tool Invocation
```json
{
  "type": "tool_invoke",
  "tool_id": "hank-chores",
  "method": "add_task",
  "params": {
    "task": "laundry",
    "priority": "high",
    "zone": "bedroom"
  },
  "source": "casper"
}
```

#### Notification Request
```json
{
  "type": "notification",
  "from_tool": "hank-chores",
  "title": "HANK Reminder",
  "message": "Laundry is ready to move to dryer!",
  "priority": "high",
  "actions": ["dismiss", "snooze", "mark_done"],
  "timestamp": "2025-12-30T15:30:00Z"
}
```

#### CASPER ↔ HEART Sync
```json
{
  "type": "sync_request",
  "device_id": "casper-oneplus8t",
  "last_sync": "2025-12-30T12:00:00Z",
  "queued_actions": [
    {"tool_id": "hank", "action": "complete_task", "task_id": 123}
  ],
  "battery_level": 87,
  "connectivity": "wifi"
}
```

### WebSocket Protocol (CASPER ↔ HEART)

**Connection:** `ws://heart.local:5000/casper`

**Message Flow:**
```
CASPER → HEART: {"type": "connect", "device_id": "casper-oneplus8t"}
HEART → CASPER: {"type": "ack", "tools": [...], "pending": [...]}

CASPER → HEART: {"type": "voice_input", "text": "add laundry to HANK"}
HEART → CASPER: {"type": "response", "result": "Task added", "tool": "hank"}

HEART → CASPER: {"type": "notification", "from": "hank", "message": "..."}
CASPER → HEART: {"type": "notification_ack", "action": "dismiss"}
```

---

## Tool Ecosystem

### Current Tools

#### HANK (Port 5100)
**Domain:** Chore management, household tasks

**Features:**
- Zone-based task organization
- 1-hour daily scheduling window
- Priority weighting
- Learned task durations
- Completion tracking

**Stack:** FastAPI + React + SQLite

**Status:** ✅ Implemented

#### Grocery (Port 5200)
**Domain:** Grocery shopping, pantry management

**Status:** 📋 Planned

### Future Tool Categories

**Port Allocation:**
```
5100-5199: Home management (HANK, Grocery)
5300-5399: Health/wellness
5400-5499: Work/productivity  
5500-5599: Finance
5600-5699: Social/family
```

### Tool Template

**Every new tool:**
1. Picks a port from allocation table
2. Implements Communication Contract
3. Has its own PRD document
4. Runs independently
5. Registers with HEART on startup

---

## Port Allocation

### Official Registry

| Port | Service | Status |
|------|---------|--------|
| 5000 | heart-core | ✅ Reserved |
| 5001 | heart-web-dashboard | ✅ Reserved |
| 5100 | HANK (chores) | ✅ In Use |
| 5200 | casper-core | ✅ Reserved |
| 5201 | Grocery | 📋 Planned |
| 5300-5399 | Health/Wellness tools | 🔓 Available |
| 5400-5499 | Work/Productivity tools | 🔓 Available |
| 5500-5599 | Finance tools | 🔓 Available |
| 5600-5699 | Social/Family tools | 🔓 Available |

**Rules:**
- Never reuse ports
- Document in this registry before using
- Claim entire range for related tools
- Update on every new tool

---

## Tech Stack

### Locked Standards

**Backend (All Tools):**
- Python 3.11+
- FastAPI
- SQLAlchemy
- SQLite (local) or PostgreSQL (if needed)
- WebSockets for real-time

**Frontend (Web Dashboards):**
- React 18+
- Vite
- Tailwind CSS
- TypeScript

**Mobile (CASPER):**
- Flutter (recommended) or React Native
- Dart or TypeScript
- Native compilation

**Communication:**
- REST APIs (JSON)
- WebSocket (real-time)
- HTTP/2 where supported

### Android-Specific

**CASPER-OS ROM:**
- LineageOS 21 base (Android 14)
- AOSP build tools
- Custom vendor tree
- Makefile-based builds

**CASPER Services:**
- Kotlin/Java (Android services)
- Termux (Python environment)
- SQLite (local storage)

---

## Development Roadmap

### Phase 1: Foundation (Weeks 1-2)
**Goal:** Get build environment ready

- [x] Create GH0STL0VE organization
- [x] Create all repositories
- [ ] Set up casperdev Mac user
- [ ] Install Android build tools
- [ ] Initialize LineageOS source (~100GB download)
- [ ] Build stock LineageOS for OnePlus 8T
- [ ] Test flash on device (when it arrives)

### Phase 2: Documentation (Weeks 1-3)
**Goal:** Complete specifications

- [ ] Write all architecture docs
- [ ] Design Communication Contract v2
- [ ] Create UI/UX guidelines
- [ ] Write build guides
- [ ] Document flash process

### Phase 3: HEART Development (Weeks 2-4)
**Goal:** Build MacBook hub

- [ ] Implement heart-core daemon
- [ ] Tool registry system
- [ ] Message router
- [ ] CASPER gateway (WebSocket server)
- [ ] Build web dashboard
- [ ] Test with HANK

### Phase 4: CASPER Services (Weeks 3-6)
**Goal:** Backend services working

- [ ] Implement casper-core daemon
- [ ] Communication Contract client
- [ ] Voice processing pipeline
- [ ] Tool invocation system
- [ ] HEART sync protocol
- [ ] Test in Termux on phone

### Phase 5: CASPER-OS ROM (Weeks 4-8)
**Goal:** Custom ROM basics

- [ ] Create vendor/casper tree
- [ ] Custom boot animation
- [ ] Remove unwanted apps
- [ ] Add CASPER branding
- [ ] Build and flash
- [ ] Verify boot

### Phase 6: Launcher & UI (Weeks 6-10)
**Goal:** Native Android interface

- [ ] Build CASPERLauncher app
- [ ] Tool grid interface
- [ ] Set as mandatory launcher
- [ ] Voice activation UI
- [ ] Status displays
- [ ] Gesture controls

### Phase 7: Integration (Weeks 8-12)
**Goal:** Everything connected

- [ ] CASPER ↔ HEART communication
- [ ] Tool registration flow
- [ ] Notification system
- [ ] Voice command routing
- [ ] Offline mode handling
- [ ] Sync mechanisms

### Phase 8: Polish (Weeks 12+)
**Goal:** Production ready

- [ ] Battery optimization
- [ ] Performance tuning
- [ ] UI refinement
- [ ] Error handling
- [ ] OTA update system
- [ ] User testing

---

## Repository Structure

### GitHub Organization: GH0STL0VE

**Core Repositories:**

```
GH0STL0VE/
├── casper-os-docs                 # 📋 This document + all specs
├── android_vendor_casper          # 🤖 Vendor tree for ROM
├── casper-os-manifest             # 📦 Repo manifest for building
│
├── CASPERLauncher                 # 📱 Android launcher app
├── CASPERServices                 # ⚙️  System services (Kotlin)
├── CASPERCore                     # 💎 Frontend app (Flutter)
│
├── casper-core-daemon             # 🐍 Python backend for CASPER
├── casper-heart-daemon            # 🖥️  HEART hub service
├── casper-heart-dashboard         # 🌐 Web UI for HEART
│
├── casper-tool-template           # 🛠️  Template for new tools
└── casper-tool-hank               # 🧹 HANK integration
```

**Development Workflow:**

1. **Docs first:** Everything starts in casper-os-docs
2. **Spec before code:** Write PRD/spec, then implement
3. **Branch strategy:** `main` (stable), `develop` (active work)
4. **Testing:** Required before merge to main
5. **Documentation:** Update docs with every feature

---

## Design Philosophy

### For Users with ADHD

**"Loud" Notifications:**
- Maximum vibration intensity
- Persistent alerts
- Cannot be easily dismissed
- Multi-modal (vibration + sound + visual)

**Minimal Friction:**
- One-tap actions where possible
- Voice-first for speed
- No unnecessary confirmations
- Smart defaults

**Radical Ownership:**
- Tools proactively manage their domains
- Anticipate needs before asking
- Surface relevant info automatically
- Reduce decision fatigue

**Always Present:**
- CASPER is in your pocket
- HEART is always running at home
- Seamless sync between them
- Never lose context

### Visual Design

**Color Palette:**
```
Backgrounds:
  #0a0a0f (darkest)
  #12121a (medium dark)
  #1a1a24 (lighter dark)

Text:
  #e8e8ed (primary white)
  #8888a0 (secondary gray)

Accents:
  CASPER: #7c5cff (violet)
  HEART:  #f472b6 (pink)
  HANK:   #34d399 (emerald)
  Grocery: #fbbf24 (amber)
```

**Typography:**
- Sans-serif, high contrast
- Minimum 16px for body text
- Bold for important actions
- Clear hierarchy

**Spacing:**
- Generous tap targets (48dp minimum)
- Breathing room between elements
- Consistent grid system

---

## Security & Privacy

### CASPER-OS

**Locked Down:**
- Bootloader re-locked after ROM flash (optional)
- No Google services (no tracking)
- No Play Store (no random app installs)
- Single-purpose device

**SELinux:**
- Enforcing mode
- Custom policies for CASPER services
- Tool isolation

### Data

**Local First:**
- All data stored locally on devices
- No cloud dependencies (except Claude API)
- Sync only over local WiFi
- Encrypted at rest (Android encryption)

**HEART Storage:**
- SQLite databases in ~/heart/data/
- Regular backups to Time Machine
- No external API calls (except Claude)

---

## Conventions

### Naming

- **Organizations:** GHOSTLOVE (all caps with 0s)
- **Tools:** ALL CAPS acronyms (CASPER, HEART, HANK)
- **Files:** kebab-case (`heart-specification.md`)
- **Code:** snake_case (Python), camelCase (TypeScript/Dart)
- **Repos:** kebab-case with prefix (`casper-core-daemon`)

### Documentation

- **Specs:** Comprehensive technical documents
- **PRDs:** Product requirements for tools
- **READMEs:** Quick start + links to specs
- **Code comments:** Explain *why*, not *what*

### Git Workflow

```bash
# Feature branch
git checkout -b feature/casper-voice-ui

# Work, commit
git add .
git commit -m "Add voice activation button to launcher"

# Push, PR
git push origin feature/casper-voice-ui
# Create PR to develop branch
# Review, test, merge

# Release
# When develop is stable, merge to main
# Tag release: v1.0.0
```

---

## Quick Reference

### When starting new work:

| Task | Read This |
|------|-----------|
| New tool | Communication Contract, Tool Template |
| CASPER UI | Design Bible, Launcher Spec |
| HEART service | HEART Architecture, Communication Contract |
| ROM modification | CASPER-OS section, Build Guide |
| General architecture | This document (Master Spec) |

### Common Commands

**Build ROM:**
```bash
cd ~/casper-os-build
source build/envsetup.sh
breakfast kebab
brunch kebab
```

**Flash Device:**
```bash
adb reboot bootloader
fastboot flashall
```

**Start HEART:**
```bash
cd ~/heart
source venv/bin/activate
python heart-core.py
```

**Start CASPER (Termux):**
```bash
cd ~/casper
python casper-core.py
```

---

## Status & Next Actions

### Immediate (This Week)

1. ✅ Create GitHub organization (GH0STL0VE)
2. ✅ Create all repositories
3. ⏳ Set up Mac development user
4. ⏳ Initialize this master spec in casper-os-docs
5. ⏳ Start LineageOS source sync (big download)

### Short Term (Weeks 1-2)

1. Complete all documentation
2. Build HEART web dashboard
3. Design CASPERLauncher UI
4. Write Communication Contract v2 spec
5. Build stock LineageOS and test

### Medium Term (Weeks 3-8)

1. Build custom CASPER-OS ROM
2. Implement HEART services
3. Implement CASPER services
4. Create native launcher app
5. Test integration

### Long Term (Months 2-3)

1. Polish UX
2. Add more tools
3. Optimize battery life
4. OTA update system
5. User testing & iteration

---

## Resources

### Documentation
- **LineageOS Build Guide:** https://wiki.lineageos.org/devices/kebab/build
- **Android Source:** https://source.android.com/
- **FastAPI Docs:** https://fastapi.tiangolo.com/
- **Flutter Docs:** https://flutter.dev/docs

### Hardware
- **OnePlus 8T (kebab) XDA:** https://forum.xda-developers.com/c/oneplus-8t.11579/
- **LineageOS Device Page:** https://wiki.lineageos.org/devices/kebab/

### Community
- **GH0STL0VE Repos:** https://github.com/GH0STL0VE
- **Project Discussions:** GitHub Issues & Discussions

---

## Appendix

### Glossary

- **CASPER:** Portable AI assistant device
- **HEART:** Home hub for tool orchestration
- **GH0STL0VE:** Organization name (Ghost + Love)
- **kebab:** OnePlus 8T device codename
- **Communication Contract:** Protocol for tool communication
- **Tool:** Domain-specific AI agent (HANK, Grocery, etc.)
- **casper-core:** Main CASPER daemon service
- **heart-core:** Main HEART orchestration service

### Version History

- **v1.0** (Dec 30, 2025): Initial master specification
  - Consolidated all architecture decisions
  - OnePlus 8T hardware selection
  - LineageOS-based custom ROM approach
  - Complete tech stack defined
  - Repository structure established

---

**End of Master Specification**

*For questions, updates, or contributions, see the GH0STL0VE organization on GitHub.*

🖤 **Ghost + Love = Home** 🖤
