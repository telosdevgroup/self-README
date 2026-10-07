---
title: "avabatt"
type: "system"
tags: [linux, systems-programming, battery-management, kernel-interfaces, sysfs, hardware-longevity, bash]
status: "stable"
last_updated: 2026-10-06
repo: "https://github.com/telosdevgroup/avabatt"
suite_url: "https://avathings.com"
---

# avabatt

> **Part of the [AvaThings Suite](avathings-web.md).**  
> **Native Linux battery lifespan preservation & hardware charge threshold manager.**  
> Zero third-party dependencies, zero daemon overhead. Pure POSIX bash interfacing directly with kernel `sysfs` power supply and platform driver nodes to protect Li-ion/Li-poly cells from high-voltage saturation and thermal degradation.

```
       +-------------------------------------------------------+
       |               CLI Operator Surface                    |
       |     (avabatt status | on [80%] | off [100%] | <N>)    |
       +---------------------------+---------------------------+
                                   | Root actuation
                                   v
       +-------------------------------------------------------+
       |       Kernel Hardware Precedence Resolution           |
       |  1. Mainline Linux kernel power supply (v5.4+)        |
       |  2. ThinkPad ACPI (thinkpad_acpi / tp_smapi)          |
       |  3. Lenovo Platform Driver (VPC2004 conservation_mode)|
       +---------------------------+---------------------------+
                                   | Direct sysfs writes
                                   v
       +-------------------------------------------------------+
       |             Embedded Controller (EC)                  |
       |  - Halts current at threshold ceiling (e.g. 80%)      |
       |  - Enables pure AC pass-through power                 |
       |  - Prevents 79% <-> 80% hysteresis micro-cycling      |
       +-------------------------------------------------------+
```

---

## 1. Problem Space & Electrochemical Rationale

Most laptops permanently docked or plugged into AC chargers remain saturated at 100% State of Charge (SoC). 

For Lithium-ion and Lithium-polymer chemistries, prolonged high-voltage saturation (**4.2V–4.35V per cell**) combined with internal laptop thermal buildup accelerates chemical decomposition of the electrolyte and cathode, leading to rapid capacity loss and irreversible cell swelling.

### Engineering Goals
- **Eliminate Heavy Power Daemons**: Replace bulky suites (e.g., TLP, power-profiles-daemon configurations) with an instant, dependency-free kernel actuator.
- **AC Pass-Through Preservation**: Command the laptop's Embedded Controller (EC) to halt battery charging at 80% (or custom thresholds) and switch entirely to AC pass-through.
- **Hysteresis Micro-Cycle Defense**: Configure paired stop/start thresholds (e.g., end at 80%, start at 75%) to prevent continuous charge toggling between 79% and 80%.

---

## 2. Kernel Interfaces & Precedence Engine

`avabatt` queries and actuates hardware nodes via strict precedence resolution:

| Precedence | Hardware Interface | Target `sysfs` Path | Actuation Mechanics |
| :--- | :--- | :--- | :--- |
| **1. Mainline Kernel** | Standard Power Supply API (Linux 5.4+) | `/sys/class/power_supply/BAT*/charge_control_end_threshold`<br>`/sys/class/power_supply/BAT*/charge_control_start_threshold` | Sets end threshold (e.g. 80%) and start threshold (e.g. 75%) directly on the battery class driver. |
| **2. ThinkPad ACPI** | `thinkpad_acpi` / `tp_smapi` | `/sys/class/power_supply/BAT*/charge_stop_threshold`<br>`/sys/class/power_supply/BAT*/charge_start_threshold` | Direct control on IBM/Lenovo enterprise hardware. |
| **3. Platform Driver** | Lenovo IdeaPad/Legion/Yoga (`VPC2004`) | `/sys/bus/platform/devices/VPC2004:*/conservation_mode` | Writes `1` to toggle firmware-level 75–80% conservation ceiling; `0` to restore 100%. |

---

## 3. Operational Mechanics

- **Drain-Down Settle**: If invoked when battery charge is above the specified threshold (e.g., set to 80% while at 95%), charging immediately halts (`Not charging`). The laptop operates off battery/pass-through until natural drain reaches 80%, where it settles.
- **NVRAM Persistence**: Leverages native EC state retention across warm/cold reboots; trivial to schedule via one-shot systemd service or `/etc/rc.local` on firmware that resets on cold boot.
- **Pure POSIX Bash**: Zero runtime requirements beyond standard core utilities. Runs in minimal recovery environments, containers, and headless minimal distros.

---

## 4. Systems Profile

| Attribute | Specification |
| :--- | :--- |
| **Runtime Footprint** | Pure POSIX Bash (`0 MB` daemon RAM, zero background processes) |
| **Dependencies** | None (Linux kernel `sysfs` native) |
| **Supported Hardware** | Intel / AMD laptops across mainline Linux, ThinkPad, IdeaPad/Legion/Yoga |
| **Key Invariant** | Strict AC pass-through enforcement; electrochemical high-voltage defense |
