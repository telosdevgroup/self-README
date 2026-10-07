# 🔋 avabatt

**A small command that caps how full your laptop battery charges.** Stopping at 80% instead of 100% is gentler on lithium cells, so the battery lasts longer. I use it on my desk machines that live on AC power.

Pure Bash. No Python, no pip, no background daemon like TLP.

---

## ⚡ Install

```bash
curl -sSL https://raw.githubusercontent.com/telosdevgroup/avabatt/main/install.sh | bash
```

Source: [github.com/telosdevgroup/avabatt](https://github.com/telosdevgroup/avabatt)

## 🕹️ Commands

```bash
avabatt status            # charge level, limits, hardware state
avabatt status --json     # same, for scripts
sudo avabatt on           # 80% ceiling (my desk default)
sudo avabatt off          # back to 100%
sudo avabatt 80           # stop at 80%, start threshold set 5% lower
sudo avabatt 100          # full charge, e.g. before travel
sudo avabatt 50 75        # custom: start at 50%, stop at 75%
sudo avabatt apply        # re-apply saved settings from /etc/avabatt.conf
```

## 🔌 Good to know

- On AC, a capped battery just sits there. It does not drain itself down to your range.
- To get into a 50–75% window, unplug and run on battery until you're below the stop level, then plug back in.
- Settings survive reboot and suspend/resume via the included `avabatt.service` systemd unit.

---

## 🔧 How it works

The Linux kernel exposes charge limits as files under sysfs. avabatt writes your start and stop thresholds to those files and saves them in `/etc/avabatt.conf`. The systemd unit runs `avabatt apply` at boot and after resume, because some firmware forgets the values.

It needs a laptop whose firmware supports charge thresholds. TODO(dev): confirm which models I've tested on.

## 🧩 Part of a set

- [avathings-applet](avathings-applet.md): panel applet that controls this from the taskbar.
- [changestate](changestate.md): the sibling tool for CPU/GPU capacity.
- [avathings-web](avathings-web.md): the website documenting all of them.
