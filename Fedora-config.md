Before running any Command make changes to config file:
- run: sudo nano /etc/dnf/dnf.conf
- Make the following changes:
  	gpgcheck=1
	installonly_limit=3
	clean_requirements_on_remove=True
	best=False
	skip_if_unavailable=True
	max_parallel_downloads=10
- to write changes: Ctrl+O , then Ctrl+X to exit

Run the following without even thinking:
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

 Install the following extensions (must have) :
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
 
 Furthur settings to change:
 - turn on right click under settings
 - Change Wallpaper
 - Turn On wallpaper
 - Change Screenshot Shortcut from keyboard shortcut settings 
 - Customise your terminal (shortcuts , colours)
 - go to firefox About:config -> apz.touch_acceleration_factor_y set to 0.4 (fix scrolling)
 