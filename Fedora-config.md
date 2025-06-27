## Before running any Command make changes to config file:
```bash
  sudo nano /etc/dnf/dnf.conf
  ```
Then paste the following into the file:
```bash
  gpgcheck=1
  installonly_limit=2
  clean_requirements_on_remove=True
  best=False
  skip_if_unavailable=True
  fastestmirror=True
  max_parallel_downloads=10
  ```
to write changes: Ctrl+O , then Ctrl+X to exit
## Run these one by one without even thinking:
```bash
    -  sudo dnf -y clean all
    -  sudo dnf -y update
    -  sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
    -  sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing
    -  sudo dnf group install multimedia
    -  flatpak install flathub com.mattjakeman.ExtensionManager
    -  sudo dnf install fwupd
    -  sudo fwupdmgr get-updates
    -  sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
    -  sudo systemctl disable NetworkManager-wait-online.service
    -  sudo dnf remove rythmbox
```

## Install the following extensions (from Gnome Extensions) :
 - Blur my shell
 - clipboard Indicator
 - Dash to Dock
 - Just Perfection
 - LockScreen Extension
 - unblank Lock Screen 
 - Vitals
 - Gnome Tweaks
 - SearchLight


## Installing Apps and Configuring Git:

1. Configure Git globally (Should already be installed)
   ```bash
   git config --global user.name "<your name>" && git config --global user.email "<your email>"
   ```
2. Install Motrix ( An open source Download Manager)
   ```bash
   flatpak install flathub net.agalwood.Motrix
   ```
3. Install Telegram
   ``` bash
   flatpak install flathub org.telegram.desktop
   ```
4. Install and configure MPV :
   ```bash
   flatpak install flathub io.mpv.Mpv
   ```
   To configure your MPV go to : ```.var/app/io.mpv.Mpv/config/mpv```    in your system
   <br>
   > If the subfolders "scripts" and "fonts" do not exist create them.
   
    - Paste the file [mpv_linux.conf](https://github.com/AntareepDey/dev-setup/blob/main/mpv_linux.conf) and rename it to ```mpv.conf```
    - Paste the files : [modernz.conf](https://github.com/AntareepDey/dev-setup/blob/main/modernz.conf) and [modernz.lua](https://github.com/AntareepDey/dev-setup/blob/main/modernz.lua) into the folder: ```scripts```
    - Paste the file : [fluent-system-icons.ttf](https://github.com/AntareepDey/dev-setup/blob/main/fluent-system-icons.ttf)  in the folder ```fonts``` 
 
## Furthur settings to change:
 
1. Remove Libre office
```bash
   sudo dnf remove libreoffice*
```

2. Make your Terminal Transparent (only if using Gnome Terminal or similar)
   You can get the identifier in the terminal settings under profile 
```bash
   dconf write /org/gnome/Ptyxis/Profiles/<identifier>/opacity 0.9
```

3. Check if system has fastboot enabled in UEFI
```bash
   [ -d /sys/firmware/efi ] && echo "UEFI" || echo "BIOS"
```   
4. Turn on right click under settings if using laptop.
5. Change Screenshot Shortcut from keyboard shortcut settings 
6. Customise your terminal (shortcuts , colours)
7. go to firefox About:config -> apz.touch_acceleration_factor_y set to 0.4 (fix scrolling)
8. Optimize battery : [video](https://www.youtube.com/watch?v=GDdGK8Z_qzs) ,[article](https://knowledgebase.frame.work/optimizing-fedora-battery-life-r1baXZh)

9. Auto Switch Power modes :
Check : https://github.com/gridhead/switcheroo
```bash
dbus-monitor --system "type='signal',path='/org/freedesktop/UPower/devices/battery_BAT0',member='PropertiesChanged'" | while read LINE; do
    echo ${LINE} | grep battery_BAT0 | grep -q PropertiesChanged
    if [ $? -eq 0 ]; then
        BATT_STAT=$(dbus-send --print-reply=literal --system --dest=org.freedesktop.UPower /org/freedesktop/UPower/devices/battery_BAT0 org.freedesktop.DBus.Properties.Get  string:org.freedesktop.UPower.Device string:State | awk '{ print $3; }')
        if [ $BATT_STAT -eq 1 ] || [ $BATT_STAT -eq 4 ]; then
            LEVEL=$(powerprofilesctl list | grep -q performance && echo "performance" || echo "balanced")
        elif [ $BATT_STAT -eq 5 ]; then
            LEVEL="balanced"
```


## Startup optimizations :

1. check startup time and analyse :
   ```bash
   systemd-analyze
   systemd-analyze blame
   ```

2. Optimize startup by masking systemd-udev-settle [Why disable this?](https://www.freedesktop.org/software/systemd/man/systemd-udev-settle.service.html):
    ```bash
    sudo systemctl mask systemd-udev-settle
    ```



##  Configure Swappiness

**Check the Current Swappiness Value**
```bash
cat /proc/sys/vm/swappiness
```
<br>

>[!Important] 
> Proceed only if your system has >8GB ram and the swappiness shown is 60 (Both must be true).


**Create a New sysctl Configuration File:**
It's recommended to create a new configuration file in `/etc/sysctl.d/` rather than editing `/etc/sysctl.conf` directly. This approach keeps custom settings organized and avoids conflicts with package updates.

```bash
sudo nano /etc/sysctl.d/99-swappiness.conf
```
In the opened file, add the following line (new fedora versions should already have this):

```bash
vm.swappiness=10
```
Save and exit the editor (`Ctrl+O`, `Enter`, then `Ctrl+X`).

Apply the Changes Immediately:
```bash
sudo sysctl --system
```

## Other important commands :

Trim SSD :
```bash 
sudo fstrim -av
```

Clears cache :
```bash
sudo dnf clean all
```

Remove unused Packedges :
```bash
sudo dnf autoremove
```

## App Configurations :

1. If Using edge, to enable wayland based features like pinch to zoom.
   ```bash
     nano ~/.var/app/com.microsoft.Edge/config/edge-flags.conf
   ```
   Then write the following inside the file: 
    ```bash
    --ozone-platform=wayland
    --enable-features=UseOzonePlatform
    ```  
