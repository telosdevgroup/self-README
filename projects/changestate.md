# ⚡ ChangeState

An automatic resource manager for Linux. It keeps machines cooler and quieter by capping CPU clocks, and it speeds back up when you start working. No third-party dependencies.

I wrote it because my hardware ran hot and loud doing nothing much. It's also what keeps the [AvaScry](avascry.md) laptop happy.

Source: `tdg/compstate` (public repo: github.com/telosdevgroup/changestate). It's part of the AvaThings suite, see [avathings-web.md](avathings-web.md).

## 🚀 Install

```bash
curl -sSL https://raw.githubusercontent.com/telosdevgroup/changestate/main/install.sh | bash
sudo systemctl enable --now changestate-auto
```

That puts the CLI at `/usr/local/bin/changestate` and starts the background service.

## 🤖 What it does on its own

It watches keyboard and mouse activity through X11 idle hooks.

- **Working:** climbs up to `P:23`, about 75% capacity, turbo off.
- **Walk away:** after a short 2–3 minute leash, it drops back down.
- **Come back:** touch a key and it snaps to `P:11`, about 35%.
- **Limits:** it stays between `P:7` and `P:23`. It never goes to `P:0` or `P:31` unless you say so.

```bash
journalctl -u changestate-auto -f       # watch it work
sudo systemctl stop changestate-auto    # turn it off
```

## 🪜 The P-scale

| Level | Meaning |
| :--- | :--- |
| `P:0` | Lockdown, lowest power. Needs deliberate confirmation. |
| `P:7` | Idle floor. |
| `P:11` | Wake-up level, ~35%. |
| `P:23` | Everyday ceiling, ~75%, quiet. |
| `P:31` | Full speed, uncapped boost. For compile jobs and heavy runs. |

## 🧰 Beyond one laptop

Ansible playbooks, cron and systemd timers, and GitLab CI recipes (a build runner steps to `P:31`, then resets) are in the repo's `docs/`. It runs headless over SSH on Ubuntu, Debian, RHEL, Rocky, Alma, Fedora and Arch. TODO(dev): confirm which distros you've actually tested.

## 🔧 How it works

- **CPU:** writes to `/sys/devices/system/cpu/*` to take cores offline, clamp the frequency ceiling, and toggle turbo.
- **Firmware:** switches ACPI profiles through `/sys/firmware/acpi/platform_profile`.
- **GPU:** limits power and clocks on NVIDIA (CLI) and AMD (`amdgpu` sysfs).
- **Steps:** the level changes follow a fixed cadence of prime numbers (7, 11, 23, 31) instead of reacting to every CPU spike. That means fewer fan rev-ups.
- **Desktop:** there's a Cinnamon applet for the tray.

Deeper docs live in the repo: `docs/architecture.md`, `docs/hardware-actuation.md`, `docs/harmonic-cadence.md`, `docs/node-status-json.md`.
