## Before running any Command make changes to config file:
- ```bash
  sudo nano /etc/dnf/dnf.conf
  ```
Make the following changes:
- ```bash
  gpgcheck=1
  installonly_limit=3
  clean_requirements_on_remove=True
  best=False
  skip_if_unavailable=True
  max_parallel_downloads=10
  ```
to write changes: Ctrl+O , then Ctrl+X to exit
- Run the following without even thinking:
```bash
    -  sudo dnf -y clean all
    -  sudo dnf install -y https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
    -  sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
    -  sudo dnf group update core
    -  sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing
    -  sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
    -  sudo systemctl disable NetworkManager-wait-online.service
    -  sudo dnf remove libreoffice*
    -  sudo dnf group install multimedia
    -  sudo dnf -y update
    -  flatpak install flathub com.mattjakeman.ExtensionManager 
    - dconf write /org/gnome/Ptyxis/Profiles/<identifier>/opacity 0.9
```

 ## Install the following extensions (must have) :
 - Blur my shell
 - clipboard Indicator
 - Dash to Dock
 - Gnome 4xUI improvements
 - Just Perfection
 - LockScreen Extension
 - unblank Lock Screen 
 - Vitals
 - Gnome Tweaks
 - screenshot-tool (Set shortcut)
 - SearchLight
 
 ## Furthur settings to change:
 - Check if system has fastboot enabled in UEFI
 - turn on right click under settings
 - Change Wallpaper
 - Turn On wallpaper
 - Change Screenshot Shortcut from keyboard shortcut settings 
 - Customise your terminal (shortcuts , colours)
 - go to firefox About:config -> apz.touch_acceleration_factor_y set to 0.4 (fix scrolling)
 - Check how to turn on SSD trimming
 - Optimize battery : [video](https://www.youtube.com/watch?v=GDdGK8Z_qzs) ,[article](https://knowledgebase.frame.work/optimizing-fedora-battery-life-r1baXZh)

- Auto Switch Power modes :
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
3. clears cache : ``` sudo dnf clean all```

4. Optimize startup by masking systemd-udev-settle:
```bash
sudo systemctl mask systemd-udev-settle
```
[Why disable this?](https://www.freedesktop.org/software/systemd/man/systemd-udev-settle.service.html)

4.Optimize startup by disabling NetworkManager-wait-online.service:
```bash
sudo systemctl disable NetworkManager-wait-online.service
```
[Why?](https://askubuntu.com/questions/1018576/what-does-networkmanager-wait-online-service-do)

