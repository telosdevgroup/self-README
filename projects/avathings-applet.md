---
title: "avathings-applet"
type: "system"
tags: [linux, desktop-environments, cinnamon, cinnamon-spices, javascript, async-gio, hardware-telemetry, ui]
status: "stable"
last_updated: 2026-10-06
repo: "https://github.com/telosdevgroup/avathings-applet"
suite_url: "https://avathings.com"
---

# avathings-applet

> **Desktop companion for the [AvaThings Suite](avathings-web.md).**  
> **Native Cinnamon Panel Applet for Real-Time Hardware Telemetry and Control.**  
> Panel UUID: `avathings@telosdevgroup`. Integrates `changestate` (compute capacity) and `avabatt` (battery threshold) directly into the Linux Mint taskbar with asynchronous GIO subprocess polling.

```
+-------------------------------------------------------------+
|                    Cinnamon Panel (Mint)                    |
|       [ P:23 | 78% (Cap 80%) | changestate: auto ON ]       |
+------------------------------+------------------------------+
                               |
            +------------------+------------------+
            | Click / Flyout Menu                 |
            v                                     v
+-----------------------+             +-----------------------+
|  Compute Governor     |             |  Battery Preserver    |
| - Slider: P:2 -> P:31 |             | - Desk Mode (80%)     |
| - changestate-auto    |             | - Full Travel (100%)  |
|   toggle (ON/OFF)     |             | - Custom Threshold    |
+-----------+-----------+             +-----------+-----------+
            |                                     |
            +------------------+------------------+
                               | Async Gio.Subprocess
                               v
+-------------------------------------------------------------+
|                     Underlying Hardware                     |
|         (/usr/local/bin/changestate | /usr/local/bin/avabatt)|
+-------------------------------------------------------------+
```

---

## 1. Architectural Problem: Desktop Panel Responsiveness

Querying hardware sysfs nodes or invoking shell utilities directly from a desktop shell extension can easily introduce UI stutter or micro-freezes if executed synchronously on the Cinnamon main event loop.

### Engineering Solutions
- **Non-Blocking Telemetry over Gio**: All status queries and telemetry polling run via asynchronous `Gio.Subprocess` calls, completely decoupling hardware I/O latency from Cinnamon UI frame rendering.
- **Polkit-Free Sudo Delegation**: Includes automated sudoers hardening (`scripts/setup-sudoers.sh`) so users can switch compute tiers and battery thresholds instantly without disruptive password prompt modals.

---

## 2. Feature Profile

| Capability | Implementation | Benefit |
| :--- | :--- | :--- |
| **Real-Time Display** | Live panel label rendering | Instant feedback on active tier (`P:23`), battery SoC, and active threshold limit. |
| **Compute Governor Control** | Flyout menu with prime tiers (`P:2` to `P:31`) | Immediate actuation of CPU core down-regulation and clock clamping. |
| **Autonomous Daemon Toggle** | `changestate-auto` state switch | Enables or pauses the background activity-monitoring daemon on demand. |
| **Battery Threshold Modes** | Desk Mode (80%) vs. Full Travel (100%) | One-click hardware EC charge halt to prevent lithium saturation. |
| **Custom Profiles** | Arbitrary start/stop limits (e.g. 50%–75%) | Tailored thresholds for specialized charging workflows. |

---

## 3. Packaging & Installation

Packaged for Cinnamon Spices standards:

```bash
# Clone and install applet to ~/.local/share/cinnamon/applets/
./scripts/install-applet.sh
./scripts/enable-applet.sh

# (Optional) Passwordless sudo rules for seamless tier switching
sudo ./scripts/setup-sudoers.sh
```

- **Panel UUID**: `avathings@telosdevgroup`
- **Source Tree**: `applet/avathings@telosdevgroup`
