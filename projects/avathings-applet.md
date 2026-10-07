# 🖥️ avathings applet

**A Cinnamon panel applet that shows and controls my power tools without opening a terminal.** It sits in the Linux Mint taskbar, shows the current state, and lets me change it in one click.

It talks to two tools: [changestate](changestate.md) (CPU/GPU capacity) and [avabatt](avabatt.md) (battery charge limit).

---

## ⚡ Install

```bash
./scripts/install-applet.sh     # copies to ~/.local/share/cinnamon/applets/
./scripts/enable-applet.sh

# optional: passwordless sudo for the specific commands it runs
sudo ./scripts/setup-sudoers.sh
```

- Applet UUID: `avathings@telosdevgroup`
- Source: `applet/avathings@telosdevgroup` in the repo

## 👀 What you get

| In the panel | What it does |
| :--- | :--- |
| Live label, e.g. `P:23` | Shows the active capacity tier, battery level and charge limit |
| Tier menu | Pick a changestate tier, `P:2` up to `P:31` |
| Auto toggle | Start or pause the `changestate-auto` daemon |
| Desk / Travel | Battery cap at 80% or 100% |
| Custom limits | Any start/stop pair, like 50–75% |

---

## 🔧 How it works

Reading hardware state inside the panel process can make the desktop stutter. So the applet never blocks: it runs status checks as async `Gio.Subprocess` calls and updates the label when they return.

Changing a tier or threshold needs root. `setup-sudoers.sh` adds narrow sudoers rules for those commands, so the menu works without a password popup. Read that script before running it; it edits sudo config.

The repo also has a `spice-package` folder for Cinnamon Spices packaging; it has been submitted upstream and is awaiting review.
