---
title: "ChangeState"
type: "system"
tags: [linux, systems-programming, kernel-interfaces, power-management, hardware-tuning, automation]
status: "stable"
last_updated: 2026-10-06
repo: "https://github.com/telosdevgroup/changestate"
suite_url: "https://avathings.com"
---

# ChangeState

> **Part of the [AvaThings Suite](avathings-web.md).**  
> **Autonomous, prime-cadenced hardware actuation and thermal envelope manager for Linux.**  
> Zero third-party dependencies. Directly interfaces with sysfs, ACPI platform profiles, and GPU drivers to downregulate compute and maintain quiet, efficient thermals.

```
       +-------------------------------------------------------+
       |                  Client Surfaces                      |
       |  (CLI / Cinnamon Applet / Ansible / Systemd Timers)  |
       +---------------------------+---------------------------+
                                   | IPC / Signals
                                   v
       +-------------------------------------------------------+
       |               changestate-auto (Daemon)               |
       |  - X11 idle hooks / input activity monitoring         |
       |  - Harmonic prime stepping (P:7 <-> P:23 default)     |
       |  - Activity ramp-up & decaying idle leash             |
       +---------------------------+---------------------------+
                                   | Actuation
                                   v
       +-------------------------------------------------------+
       |               Kernel / Hardware Layer                 |
       |  - sysfs CPU core down-regulation & clock clamping    |
       |  - ACPI platform profile switches                     |
       |  - AMD & NVIDIA GPU driver thermal/clock actuation    |
       +-------------------------------------------------------+
```

---

## 1. Problem Space & Architectural Rationale

Modern desktop and server hardware defaults to aggressive boost clocks, fan ramping, and power draw—even for mundane tasks, background processing, or idle states. Standard OS governors (`powersave`, `ondemand`, `performance`) operate with crude heuristics that swing wildly between power states.

### Core Objectives
1. **Conservative Envelope by Default**: Cap clocks at an 80% ceiling with turbo boost disabled during standard operation; preserve silicon longevity and acoustic silence.
2. **Deterministic Prime Stepping**: Stepping transitions follow a prime-cadenced curve rather than noisy threshold jitter.
3. **Zero Third-Party Dependencies**: Pure POSIX/bash and Linux kernel sysfs interfaces. No heavy runtime dependencies, Python daemons, or bloated GUI libraries required on target hosts.
4. **Desktop & Cluster Scalability**: Seamlessly operates as a background daemon on personal workstations (with GUI integration like Cinnamon applets) or across multi-node server clusters via Ansible and SSH.

---

## 2. Technical Profile & Actuation Mechanics

| Layer | Implementation | Notes |
| :--- | :--- | :--- |
| **CPU Control** | `/sys/devices/system/cpu/*` | Dynamic core online/offline actuation, strict frequency ceiling clamping, turbo boost toggle. |
| **GPU Control** | NVIDIA NVML/CLI & AMD `amdgpu` sysfs | Clamps power limits and clocks without breaking active render loops. |
| **Thermals & ACPI** | `/sys/firmware/acpi/platform_profile` | Switches low-power, balanced, and performance profiles at firmware level. |
| **Activity Tracking**| X11 idle hooks / input devices | Instant wake snap to P:11 on input; fast 2-3 minute decaying leash when user steps away. |
| **GUI Integration** | Custom Cinnamon desktop applet | Tray diagnostics, live tier inspection, quick override switches. |

---

## 3. Tier Topology (The P-Scale)

Operating modes are mapped across discrete states with distinct hardware profiles:

- **`P:0` (MOM / Metal Over Moss)**: Airgap lockdown and extreme power minimization. Requires deliberate operator confirmation.
- **`P:7`**: Low-idle floor. Minimum active cores and baseline clock frequencies.
- **`P:11`**: Instant wake snap (~35% capacity). Zero-lag interactive responsiveness when touching mouse/keyboard.
- **`P:23`**: Balanced operational ceiling (~75% capacity). Whisper-quiet acoustics with turbo boost disabled.
- **`P:31` (Salt Flats)**: 100% uncapped compute and unrestricted boost. Used for CI compile jobs or heavy compute passes.

The autonomous daemon strictly bounds autonomous adjustments within **`P:7` to `P:23`**, never crossing into lockdown (`P:0`) or thermal extremes (`P:31`) without explicit manual command.

---

## 4. Operational Surfaces & Tooling

### Rapid Setup
```bash
# 5-second deploy + autonomous daemon
curl -sSL https://raw.githubusercontent.com/telosdevgroup/changestate/main/install.sh | bash
sudo systemctl enable --now changestate-auto
```

### Automation & Remote Fleet Management
- **Ansible Playbooks**: Multi-host rolling deployments, cluster-wide tier shifting, and headless fleet orchestration.
- **Systemd Timers / Cron**: Scheduled capacity shifts (e.g., higher capacity during working hours, deep sleep off-hours).
- **CI/CD Integrations**: GitLab CI recipes enabling build runners to step to `P:31` for parallel compilation and immediately reset.
- **Sudoers Rules**: Hardened zero-password privilege delegation for automated runner actuation.

---

## 5. Architectural Takeaways

- **Reliability via Simplicity**: Interfacing directly with Linux `/sys` virtual filesystems ensures robustness across distros (Ubuntu, Debian, RHEL, Rocky, Alma, Fedora, Arch) without dependency drift.
- **Predictable State Transitions**: Replacing noisy PID controllers or naive CPU threshold polling with input hooks and bounded state tiers eliminates fan rev-ups and thermal cycling.
