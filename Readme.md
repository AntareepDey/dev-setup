# Fedora (F42+) with GNOME — Setup Guide

An opinionated setup guide I compiled to make re-installing Fedora with GNOME painless.
**Check every command before running it. Use at your own risk.**

## Contents

1. [Base System](#1-base-system)
2. [GNOME](#2-gnome)
3. [Applications](#3-applications)
4. [Performance & Power](#4-performance--power)
5. [Audio Priority (WirePlumber)](#5-audio-priority-wireplumber)
6. [Optional Tweaks](#6-optional-tweaks)
7. [Maintenance Commands](#7-maintenance-commands)

---

## 1. Base System

### 1.1 DNF config (do this first, before any other command)

```bash
sudo nano /etc/dnf/dnf.conf
```

Paste:

```ini
gpgcheck=1
installonly_limit=2
clean_requirements_on_remove=True
best=False
skip_if_unavailable=True
fastestmirror=True
max_parallel_downloads=5
```

Save with `Ctrl+O`, exit with `Ctrl+X`.

### 1.2 Updates, repos and codecs

```bash
sudo dnf -y clean all
sudo dnf -y update
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing
sudo dnf group install multimedia
```

### 1.3 Firmware, cleanup and clock

```bash
sudo dnf install fwupd
sudo fwupdmgr get-updates
sudo systemctl disable NetworkManager-wait-online.service
sudo dnf remove rhythmbox
sudo timedatectl set-local-rtc 0
```

---

## 2. GNOME

### 2.1 Disable GNOME Software

It serves no purpose other than being a resource hog.

```bash
# Create the override directory
sudo mkdir -p /etc/systemd/user/gnome-session@gnome.target.d/

# Copy the file
sudo cp /usr/lib/systemd/user/gnome-session@gnome.target.d/gnome.session.conf /etc/systemd/user/gnome-session@gnome.target.d/gnome.session.conf

# Remove the gnome-software lines (the comment + the Wants line)
sudo sed -i '/# Checking for automatic updates, etc/d; /Wants=gnome-software.service/d' /etc/systemd/user/gnome-session@gnome.target.d/gnome.session.conf

# Reload the systemd user daemon to apply changes
systemctl --user daemon-reload
```

### 2.2 Extension Manager

```bash
flatpak install flathub com.mattjakeman.ExtensionManager
```

### 2.3 Extensions to install

- Blur my Shell
- Clipboard Indicator
- Dash to Dock
- Just Perfection
- LockScreen Extension
- SearchLight

### 2.4 Restore extension settings

Download [gnome-extension-settings.dconf](https://github.com/AntareepDey/dev-setup/blob/main/gnome-extension-settings.dconf), then:

```bash
dconf load /org/gnome/shell/extensions/ < ~/gnome-extension-settings.dconf
```

---

## 3. Applications

### 3.1 Git (already installed)

```bash
git config --global user.name "<your name>" && git config --global user.email "<your email>"
```

### 3.2 Axel (CLI download manager)

```bash
sudo dnf install axel
```

Config — `nano ~/.axelrc`:

```ini
reconnect_delay = 20
num_connections = 8
max_redirect = 20
connection_timeout = 30
strip_cgi_parameters = 1
default_filename = default
save_state_interval = 10
verbose = 1
```

Usage (`cd` into the target directory first):

```bash
axel <link-to-download>
```

### 3.3 MPV

```bash
flatpak install flathub io.mpv.Mpv
```

Config lives in `.var/app/io.mpv.Mpv/config/mpv`.

> If the subfolders `scripts` and `fonts` do not exist, create them.

- [mpv_linux.conf](https://github.com/AntareepDey/dev-setup/blob/main/mpv_linux.conf) → rename to `mpv.conf`
- [modernz.conf](https://github.com/AntareepDey/dev-setup/blob/main/modernz.conf) and [modernz.lua](https://github.com/AntareepDey/dev-setup/blob/main/modernz.lua) → `scripts/`
- [fluent-system-icons.ttf](https://github.com/AntareepDey/dev-setup/blob/main/fluent-system-icons.ttf) → `fonts/`

### 3.4 Brave Origin

```bash
sudo dnf install dnf-plugins-core
sudo dnf config-manager addrepo --from-repofile=https://brave-browser-rpm-release.s3.brave.com/brave-browser.repo
sudo dnf install brave-origin
```

### 3.5 Cloudflare WARP

```bash
sudo rpm -e 'gpg-pubkey(4fa1c3ba-61abda35)' && sudo rpm --import https://pkg.cloudflareclient.com/pubkey.gpg
curl -fsSl https://pkg.cloudflareclient.com/cloudflare-warp-ascii.repo | sudo tee /etc/yum.repos.d/cloudflare-warp.repo
sudo dnf update
sudo dnf install cloudflare-warp
```

### 3.6 Zed

```bash
curl -f https://zed.dev/install.sh | sh
```

Restore the optimal settings by copying [settings.json](https://github.com/AntareepDey/dev-setup/blob/main/settings.json) to `~/.config/zed/settings.json`.

### 3.7 Flatpaks

```bash
flatpak install flathub org.telegram.desktop        # Telegram
flatpak install flathub net.nokyan.Resources        # Resources (system monitor)
flatpak install flathub com.spotify.Client          # Spotify
flatpak install flathub org.localsend.localsend_app # LocalSend
```

### 3.8 uv (Python package manager)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 3.9 Bun (JS runtime + package manager)

```bash
curl -fsSL https://bun.com/install | bash
```

---

## 4. Performance & Power

### 4.1 Startup time

Measure first:

```bash
systemd-analyze
systemd-analyze blame
```

Mask `systemd-udev-settle` ([why?](https://www.freedesktop.org/software/systemd/man/systemd-udev-settle.service.html)):

```bash
sudo systemctl mask systemd-udev-settle
```

If `dnf-makecache.service` shows up as a heavy item in `systemd-analyze blame`, delay it:

```bash
sudo mkdir -p /etc/systemd/system/dnf-makecache.timer.d
sudo tee /etc/systemd/system/dnf-makecache.timer.d/override.conf > /dev/null <<'EOF'
[Timer]
# Wait 30 minutes after boot before the first run
OnBootSec=30min
EOF

sudo systemctl daemon-reload
sudo systemctl restart dnf-makecache.timer
sudo systemctl list-timers --all | grep dnf-makecache
```

### 4.2 Swappiness

Check the current value:

```bash
cat /proc/sys/vm/swappiness
```

> [!IMPORTANT]
> Proceed only if your system has >8GB RAM **and** the swappiness shown is 60 (both must be true).

Create a new file in `/etc/sysctl.d/` rather than editing `/etc/sysctl.conf` directly — this keeps custom settings organized and avoids conflicts with package updates.

```bash
sudo nano /etc/sysctl.d/99-swappiness.conf
```

Add (newer Fedora versions should already have this):

```ini
vm.swappiness=10
```

Save (`Ctrl+O`, `Enter`, `Ctrl+X`), then apply:

```bash
sudo sysctl --system
```

### 4.3 TLP

Replaces Fedora's default `tuned` / `tuned-ppd`.

```bash
sudo dnf remove tuned tuned-ppd
sudo dnf install tlp tlp-rdw
sudo systemctl enable tlp --now
```

Replace the config with [tlp.conf](https://github.com/AntareepDey/dev-setup/blob/main/tlp.conf):

```bash
sudo nano /etc/tlp.conf
sudo systemctl restart tlp
```

### 4.4 Intel iGPU

```bash
sudo sysctl -w dev.i915.perf_stream_paranoid=0
```

To make it permanent, add `dev.i915.perf_stream_paranoid=0` to `/etc/sysctl.d/60-intel.conf` and restart:

```bash
sudo nano /etc/sysctl.d/60-intel.conf
```

### 4.5 Further battery reading

[Video](https://www.youtube.com/watch?v=GDdGK8Z_qzs) · [Article](https://knowledgebase.frame.work/optimizing-fedora-battery-life-r1baXZh)

---

## 5. Audio Priority (WirePlumber)

Set the auto-selection priority of audio outputs: **Headphones > Speakers > HDMI**.

1. List active audio devices and note the IDs under **Sinks**:

   ```bash
   wpctl status
   ```

2. Inspect their `node.name` attributes:

   ```bash
   wpctl inspect <HEADPHONES_ID> | grep 'node.name'
   wpctl inspect <HDMI_ID> | grep 'node.name'
   ```

3. Create the user config override directory:

   ```bash
   mkdir -p ~/.config/wireplumber/wireplumber.conf.d/
   ```

4. Write the priority rules:

   ```bash
   nano ~/.config/wireplumber/wireplumber.conf.d/51-device-priority.conf
   ```

   ```spa
   monitor.alsa.rules = [
     # 1. Headphones (Highest Priority)
     {
       matches = [
         {
           node.name = "~alsa_output.*HiFi__Headphones__sink"
         }
       ]
       actions = {
         update-props = {
           priority.session = 2000
         }
       }
     },

     # 2. Built-in Speakers (Medium Priority)
     {
       matches = [
         {
           node.name = "~alsa_output.*HiFi__Speaker__sink"
         }
       ]
       actions = {
         update-props = {
           priority.session = 1500
         }
       }
     },

     # 3. HDMI / DisplayPort Outputs (Lowest Priority)
     {
       matches = [
         {
           node.name = "~alsa_output.*HiFi__HDMI.*"
         }
       ]
       actions = {
         update-props = {
           priority.session = 1000
         }
       }
     }
   ]
   ```

5. Restart WirePlumber (no sudo required):

   ```bash
   systemctl --user restart wireplumber
   ```

---

## 6. Optional Tweaks

### 6.1 Swap LibreOffice for ONLYOFFICE

```bash
sudo dnf remove libreoffice*
flatpak install flathub org.onlyoffice.desktopeditors
```

### 6.2 Transparent terminal (Ptyxis / GNOME Terminal)

Get the identifier from terminal settings under Profile:

```bash
dconf write /org/gnome/Ptyxis/Profiles/<identifier>/opacity 0.9
```

### 6.3 Firefox scrolling fix

`about:config` → set `apz.touch_acceleration_factor_y` to `0.4`.

### 6.4 Check firmware mode

```bash
[ -d /sys/firmware/efi ] && echo "UEFI" || echo "BIOS"
```

### 6.5 GUI settings

- Turn on right click (laptops) in Settings.
- Change the screenshot shortcut in Keyboard Shortcut settings.
- Customise your terminal (shortcuts, colours).

---

## 7. Maintenance Commands

```bash
sudo fstrim -av      # Trim SSD
sudo dnf clean all   # Clear cache
sudo dnf autoremove  # Remove unused packages
```
