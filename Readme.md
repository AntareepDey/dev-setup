# Guide to Setup Fedora (F42+) with Gnome:
This guide has been compiled by me in order to make it easier for me to setup fedora with Gnome as a window manager on any other system in the future. This setup is opionated. Please check everything before running these on your own and at your own risk. 
<br>

### [Just After Fresh Install] Before running any Command make changes to config file: 
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
  max_parallel_downloads=5
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
    -  sudo systemctl disable NetworkManager-wait-online.service
    -  sudo dnf remove rythmbox
    -  sudo timedatectl set-local-rtc 0
```

**Fixing Gnome Software :** This software actually serves no purpose other than being resource hog. Here is how to disable it: 
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



<br>

### Install the following extensions (from Gnome Extensions) :
 - Blur my shell
 - clipboard Indicator
 - Dash to Dock
 - Just Perfection
 - LockScreen Extension
 - SearchLight

 <br>

 To quick restore my configs for these extensions, first download the [dconf](https://github.com/AntareepDey/dev-setup/blob/main/gnome-extension-settings.dconf) file in this repository, then run:
```bash
dconf load /org/gnome/shell/extensions/ < ~/gnome-extension-settings.dconf
```

<br>

### Installing Apps and Configuring Git:

1. Configure Git globally (Should already be installed)
   ```bash
   git config --global user.name "<your name>" && git config --global user.email "<your email>"
   ```
2. Install Axel (A CLI based Download Manager)
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

5. Install Zed:
   ```bash
   curl -f https://zed.dev/install.sh | sh
   ```

6. Install Resources:
   ```bash
   flatpak install flathub net.nokyan.Resources
   ```

7. Install Spotify:
   ```bash
   flatpak install flathub com.spotify.Client
   ```

8. Install Brave Origin:
	```bash
	sudo dnf install dnf-plugins-core
	
	sudo dnf config-manager addrepo --from-repofile=https://brave-browser-rpm-release.s3.brave.com/brave-browser.repo
	
	sudo dnf install brave-origin   
	```

9. Install Cloudflare warp:
   ```bash
   sudo rpm -e 'gpg-pubkey(4fa1c3ba-61abda35)' && sudo rpm --import https://pkg.cloudflareclient.com/pubkey.gpg
   curl -fsSl https://pkg.cloudflareclient.com/cloudflare-warp-ascii.repo | sudo tee /etc/yum.repos.d/cloudflare-warp.repo
   sudo dnf update
   sudo dnf install cloudflare-warp
   ```

10. Install Local Send :
   ```bash
   flatpak install flathub org.localsend.localsend_app
   ```
11. Install UV, Astro , Bun:
   ```bash

   ```

<br>
 
### [Optional] Further settings to change:
 
1. Remove Libre office
    ```bash
       sudo dnf remove libreoffice*
    ```

2. Install ONLY Office    
   ```bash
      flatpak install flathub org.onlyoffice.desktopeditors
   ```

3. Make your Terminal Transparent (only if using Gnome Terminal )
   You can get the identifier in the terminal settings under profile 
    ```bash
       dconf write /org/gnome/Ptyxis/Profiles/<identifier>/opacity 0.9
    ```

4. Check if system has fastboot enabled in UEFI
    ```bash
       [ -d /sys/firmware/efi ] && echo "UEFI" || echo "BIOS"
    ```   
5. Turn on right click under settings if using laptop.
6. Change Screenshot Shortcut from keyboard shortcut settings 
7. Customise your terminal (shortcuts , colours)
8. go to firefox About:config -> apz.touch_acceleration_factor_y set to 0.4 (fix scrolling)
9. Optimize battery : [video](https://www.youtube.com/watch?v=GDdGK8Z_qzs) ,[article](https://knowledgebase.frame.work/optimizing-fedora-battery-life-r1baXZh)

10. **Use TLP:**
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

3. If after runnning `systemmd-analyze` you get a process `dnf-makecache.service` taking up al lot of time , you can configure it to startup a lot later as compared to startup :
   ```sudo mkdir -p /etc/systemd/system/dnf-makecache.timer.d
      sudo tee /etc/systemd/system/dnf-makecache.timer.d/override.conf > /dev/null <<'EOF'
      
      #Then write the following :
      [Timer]
      # Wait 30 minutes after boot before the first run
      OnBootSec=30min
      EOF

      sudo systemctl daemon-reload
      sudo systemctl restart dnf-makecache.timer
      sudo systemctl list-timers --all | grep dnf-makecache
   ```

<br>

###  Configure Swappiness

Check the Current Swappiness Value
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

### Quality of life Configurations :

#### A . Change the auto priority of various sound sources :

1. **Inspect Audio Sinks and Node Names:** Identify your system's exact audio endpoints.
Open your terminal and list all active audio devices:

```bash
wpctl status
```

2. Note the ID numbers under the **Sinks** section, then inspect their specific `node.name` attributes:

```bash
wpctl inspect <HEADPHONES_ID> | grep 'node.name'
wpctl inspect <HDMI_ID> | grep 'node.name'

```

3. **Create the WirePlumber Configuration Directory:**
Ensure the user configuration override directory exists:

```bash
mkdir -p ~/.config/wireplumber/wireplumber.conf.d/

```


4. **Write the Priority Rules Configuration:**
Create the override file in your editor:

```bash
nano ~/.config/wireplumber/wireplumber.conf.d/51-device-priority.conf

```

Add the priority definitions (**Headphones > Speakers > HDMI**):

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

5. **Restart WirePlumber to Apply Changes:** No sudo required.
Restart the WirePlumber user service:

```bash
systemctl --user restart wireplumber

```
   <br>
