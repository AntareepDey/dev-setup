# Guide to Setup Fedora (F42 and up) :
This guide has been compiled by me in order to make it easier for me to setup fedora on any other system in the future. Most of the methods mentioned in this document have been specifically centered around my preferences and the problem i have faced in the past to setup fedora . Please Check everything before running these on your own and at your own risk. 

<br>

### Before running any Command make changes to config file:
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

<br>

### Run these one by one without even thinking:
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
    -  sudo timedatectl set-local-rtc 0
```

<br>

### Install the following extensions (from Gnome Extensions) :
 - Blur my shell
 - clipboard Indicator
 - Dash to Dock
 - Just Perfection
 - LockScreen Extension
 - Gnome Tweaks
 - SearchLight

<br>

### Installing Apps and Configuring Git:

1. Configure Git globally (Should already be installed)
   ```bash
   git config --global user.name "<your name>" && git config --global user.email "<your email>"
   ```
2. Install Axel ( An CLI based Download Manager)
   ```bash
   sudo dnf install axel
   ```
   Then use ```nano ~/.axelrc``` to open the config file , and write the following changes to it :
   ```bash
   reconnect_delay = 20
   num_connections = 8
   max_redirect = 20
   connection_timeout = 30
   strip_cgi_parameters = 1
   default_filename = default
   save_state_interval = 10
   verbose = 1
   ```
   To use it : (first cd into the directory where you want to download the file)
   ```bash
   axel <link-to-download>
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

<br>
 
### Further settings to change:
 
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

9. **Use TLP:**
- Remove tuned and tuned-ppd (default fedora power implementation) :
  ```bash
     sudo dnf remove tuned tuned-ppd
  ``` 
- Install TLP :
  ```bash
     sudo dnf install tlp tlp-rdw
  ```
- Enable TLP :
  ```bash
     sudo systemctl enable tlp --now
  ```
- Replace the config file to this : [Download](https://github.com/AntareepDey/dev-setup/blob/main/tlp.conf)
  ``` bash
      sudo nano /etc/tlp.conf
  ```
- Restart after making changes :
  ```bash
     sudo systemctl restart tlp
  ```
- Additional Intel iGPU optmization:
  ```bash
  sudo sysctl -w dev.i915.perf_stream_paranoid=0
  ```
To make it permanent go to : ```sudo nano /etc/sysctl.d/60-intel.conf```
and write this line: ```dev.i915.perf_stream_paranoid=0``` and restart

<br>

### Startup optimizations :

1. check startup time and analyse :
   ```bash
   systemd-analyze
   systemd-analyze blame
   ```

2. Optimize startup by masking systemd-udev-settle [Why disable this?](https://www.freedesktop.org/software/systemd/man/systemd-udev-settle.service.html):
    ```bash
    sudo systemctl mask systemd-udev-settle
    ```

<br>

###  Configure Swappiness

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
<br>

### Other important commands :

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
<br>

### App  Specific Configurations :

1. If Using edge, to enable wayland based features like pinch to zoom.
   ```bash
     nano ~/.var/app/com.microsoft.Edge/config/edge-flags.conf
   ```
   Then write the following inside the file: 
    ```bash
    --ozone-platform=wayland
    --enable-features=UseOzonePlatform
    ```
   If web app icons are loading under edge icon. go to .var>app>edge>data>applications. There will be number of files based on the number of webapps       you have . Remove the last few lines and modify :
   ```bash
   StartupWMClass=msedge-_<app-id>-Default
   ``` 

### Quality of Life Improvements :

#### Configure an OCR based screenshot tool using (mostly)inbuilt libraries

1. First Install the required packedges :
   ```bash 
      sudo dnf install tesseract tesseract-langpack-eng gnome-screenshot wl-clipboard
   ```

2. Create a file named "screenshot_ocr.sh" and write this to it (modify the path if need be):
   ```bash
      #!/bin/bash

      # Modify this path if needed:
      SCREENSHOT_DIR="/home/antareep/Pictures/Screenshots"
      mkdir -p "$SCREENSHOT_DIR"

      # Generate a timestamp for the filename
      TIMESTAMP=$(date +%Y%m%d_%H%M%S)

      # Define the screenshot path
      SCREENSHOT_PATH="$SCREENSHOT_DIR/screenshot_${TIMESTAMP}.png"

      # Take a screenshot of a selected area and save it to the defined path
      gnome-screenshot -a -f "$SCREENSHOT_PATH"

      # Extract text using Tesseract and pipe directly to processing without saving to a file
      # Specify English language for better accuracy and speed on English text
      tesseract "$SCREENSHOT_PATH" - -l eng |
         tr -cd '\11\12\15\40-\176' | grep . | perl -pe 'chomp if eof' |
         wl-copy
      ```   
3. Make this executable by :
   ```bash
      chmod +x path/to/screenshot_ocr.sh
   ```

4. Go to Settings > Keyboard > View and customise Shortcuts> Custom Shortcuts
   and add the `path/to/screenshot_ocr.sh` to the `Command:` box, select a shortcut and save.  