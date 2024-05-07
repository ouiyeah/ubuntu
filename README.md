# download iso

open <http://www.ubuntu.com/download/desktop/>
![download_iso](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/download_iso.png "download_iso")
![download_contribute](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/download_contribute.png "download_contribute")

use other software (e.g. ultraiso) to make boot disk if necessary

***
# install system

![install_start](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_start.png "install_start")
![select_language](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_language.png "select_language")
![select_custom](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_custom.png "select_custom")
![select_type](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_type.png "select_type")
![select_location](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_location.png "select_location")
![select_keyboard](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_keyboard.png "select_keyboard")

note that if ubuntu system was reinstalled this hostname settings page may not be presented
![select_hostname](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/select_hostname.png "select_hostname")
![install_wait](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_wait.png "install_wait")
![install_complete](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/install_complete.png "install_complete")

***
# fake monitor

set scaling factor to 2 if hi-dpi screen enabled

> gsettings set org.gnome.desktop.interface scaling-factor 2

fake a virtual monitor temperarily if gui program open failed

> xrandr --newmode "hitrobot"   49.00  1024 1072 1168 1312  600 603 613 624 -hsync +vsync

> xrandr --addmode VIRTUAL1 hitrobot

retry gui program (e.g. rviz) and good luck

add virtual monitors for ubuntu18.04

> sudo vi /usr/share/X11/xorg.conf.d/20-intel.conf

    Section "Device"
        Identifier "intelgpu0"
        Driver "intel"
        Option "VirtualHeads" "2"
    EndSection

X11 XServer Xorg without real monitor

> sudo apt install xorg-video-abi-23(not recommended)

> sudo apt install xserver-xorg-video-dummy

> sudo cp ~/Documents/xorg.conf /usr/share/X11/xorg.conf.d/

> sudo apt-get install xserver-xorg-input-all(not recommended)

```
Section "Device"
    Identifier "DummyDevice"
    Driver "dummy"
    VideoRam 256000
EndSection

Section "Screen"
    Identifier "DummyScreen"
    Device "DummyDevice"
    Monitor "DummyMonitor"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1080_60.0"
    EndSubSection
EndSection

Section "Monitor"
    Identifier "DummyMonitor"
    HorizSync 30-70
    VertRefresh 50-75
    ModeLine "1920x1080" 148.50 1920 2448 2492 2640 1080 1084 1089 1125 +Hsync +Vsync
EndSection
```

***
# configure network

bind eth0 to staic address for legacy linux network (not recommended)
> sudo vi /etc/network/interfaces

    #auto lo
    #iface lo inet loopback
    auto eth0
    iface eth0 inet static
    address 192.168.0.7
    netmask 255.255.255.0

reset eth0 to static address once for legacy linux network (not recommended)

> ifconfig eth0 [ip_address] netmask [ip_netmask] up

add connection in network manager gui mode (recommended)

![ethernet_connection](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/ethernet_connection.png "ethernet_connection")

set connection in network manager config file (strongly recommended)

> sudo vi /etc/NetworkManager/system-connections/wifi_ssid

    [connection]
    id=ChinaNet-ouiyeah
    uuid=cb9d0600-2d5f-4430-b874-9aeb67914d2f
    type=802-11-wireless / 802-3-ethernet
    autoconnect=true

    [802-3-ethernet]
    duplex=full
    mac-address=0:1d:72:37:a9:df

    [802-11-wireless]
    ssid=ChinaNet-ouiyeah
    mode=infrastructure
    mac-address=40:E2:30:C3:76:43
    security=802-11-wireless-security

    [802-11-wireless-security]
    key-mgmt=wpa-psk
    psk=Can@jingt0

    [ipv4]
    method=manual / shared
    dns=192.168.0.1;
    addresses1=192.168.0.7/24,192.168.0.1

    [ipv6]
    method=auto

remember to set the link for network connection if failed to visit websites after installation

> sudo ln -s /run/resolvconf/resolv.conf /etc/resolv.conf

> sudo apt-get install net-tools

> iwlist wlan0 scan | grep ESSID

> sudo iw dev wlan0 scan | less

***
# remote access

set remmina if it is used as terminal

> sudo apt-get install remmina

![remmina_client](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remmina_client.png "remmina_client")
![remmina_add](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remmina_add.png "remmina_add")

set desktop sharing if it is used as host

> sudo apt-get install vino

![desktop_sharing](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/desktop_sharing.png "desktop_sharing")
![desktop_settings](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/desktop_settings.png "desktop_settings")

set vnc4server for connecting ubuntu from other system terminal (e.g. windows / ubuntu)

> sudo apt-get install vnc4server

> sudo apt-get install xrdp

> sudo apt-get install dconf-editor

open dconf-editor and visit org > gnome > desktop > remote-access
![dconf_editor](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/dconf_editor.png "dconf_editor")
uncheck the "require-encryption" attribute

> sudo vi /usr/share/glib-2.0/schemas/org.gnome.Vino.gschema.xml

````
<key name='enabled' type='b'>
  <summary>Enable remote access to the desktop</summary>
  <description>
    If true, allows remote access to the desktop via the RFB
    protocol. Users on remote machines may then connect to the
    desktop using a VNC viewer.
  </description>
  <default>true</default>
</key>
````

> sudo glib-compile-schemas /usr/share/glib-2.0/schemas

> /usr/lib/vino/vino-server

set x11vnc for connecting odroid-ubuntu from windows

> sudo apt-get install x11vnc

> sudo apt-get install xrdp

> sudo vi /etc/init/x11vnc.conf

    start on login-session-start
    script
        x11vnc -display :0 -auth /var/run/lightdm/root/:0 -forever -shared -bg -o /var/log/x11vnc.log -rfbport 5900 -tightfilexfer
    end script

use remote desktop from rdp to vnc
![remote_rdp_vnc](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/remote_rdp_vnc.png "remote_rdp_vnc")

use [tightvnc](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/pkg/tightvnc-2.7.10-setup-64bit.msi) to copy and paste clipboard between windows and linux

use scp to copy files between linux systems and use [pscp](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/pkg/putty.zip) to copy files from or to windows

use [teamviewer](https://www.teamviewer.com/en/download/) to fulfill remote access worldwide

reset unity if toolbar disappeared

> dconf reset -f /org/compiz/

> setsid unity

***
# set permissions

cancel sudo password

> sudo sed -i -e "/%sudo\s*ALL=(ALL:ALL)\s*ALL/ c %sudo\tALL=(ALL:ALL) NOPASSWD:ALL" /etc/sudoers

use pkexec command if sudo failed

> pkexec visudo -f /etc/sudoers

save (ctrl^o + return) and exit (ctrl^x)

change sudo password

> sudo passwd [user]

change powerbtn event to shutdown immediately (legacy)

> sudo sed -i -e "/action=\/etc\/acpi\/powerbtn.sh/ c action=sudo /sbin/shutdown -h now" /etc/acpi/events/powerbtn

change powerbtn event to reset communication (current)

> sudo sed -i -e "/action=\/etc\/acpi\/powerbtn.sh/ c action=/etc/acpi/comm-reset.sh" /etc/acpi/events/powerbtn

> sudo ln -s ~/catkin_ws/comm-reset.sh /etc/acpi/comm-reset.sh

> sudo service acpid restart

open dconf-editor and visit org > gnome > settings-daemon > plugins > power
![dconf_button](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/dconf_button.png "dconf_button")
change the "button-power" attribute to "nothing"

add current user to dialout group for tty authority

> sudo usermod -aG dialout $(whoami)

create tty rule file for current user

> echo 'KERNEL=="ttyS[0-9]*", MODE="0666"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

> echo 'KERNEL=="ttyUSB[0-9]*", MODE="0666"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

lookup and bind the device id

> udevadm info /dev/ttyUSB0 (e.g. USB0 ID_PATH=pci-0000:00:1a.0-usb-0:1.2:1.0)

![lsusb_lookup](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/lsusb_lookup.png "lsusb_lookup")

> echo 'SUBSYSTEM=="tty", ENV{ID_PATH}=="pci-0000:00:1a.0-usb-0:1.2:1.0", SYMLINK+="alias_name(e.g.)"' | sudo tee -a /etc/udev/rules.d/70-persistent-tty.rules

revise grub file in order to skip boot-in check if necessary

> sudo sed -i '177s/ ro / rw /' /etc/grub.d/10_linux （replace 177=>184/188 for 18.04)

> sudo update-grub

***
# install softwares

install google input source for ibus (or fcitx)

> sudo apt-get install ibus-googlepinyin (or fcitx-googlepinyin)

install basic development toolkits

> sudo apt-get install vim ssh htop cutecom setserial imagemagick

set git config for user name and email

> sudo apt-get install git

> git config --global user.name \`hostname\`

> git config --global user.email $USER@hitrobotgroup.com

> git config --global credential.helper store

generate ssh-key and add ~/.ssh/id_rsa.pub to github if necessary

> ssh-keygen -t rsa -C $USER@hitrobotgroup.com

may need to add ssh only if the system isn’t doing it for you automatically.

> ssh-add ~/.ssh/id_rsa

> cat ~/.ssh/id_rsa.pub

install gitg for git and rapidsvn for svn

> sudo apt-get install gitg rapidsvn meld

link git repository

> git clone https://github.com/ros-org/release

> git clone git@github.com:ros-org/ros_org.git

fix git error if necessary

> find .git/objects/ -type f -empty | xargs rm

link svn repository if rapidsvn is failed to get permanent certification

> svn list https://10.1.11.10/svn/LaserGPS1 (e.g.)

remove all backup~ files from svn if necessary

> find . -name *~ -exec rm {} \;

set "subl" and "meld" in the preference of rapidsvn

install partition tools if necessary

> sudo apt-get install gparted

install mysql database if necessary

> sudo apt-get install mysql-server

> sudo apt-get install mysql-client

> sudo apt-get install libmysqlclient-dev

***
# auto startup

> sudo apt-get install gnome-screensaver

![startup_applications](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/startup_applications.png "startup_applications")
![edit_preferences](https://raw.githubusercontent.com/ouiyeah/ubuntu/master/img/edit_preferences.png "edit_preferences")

edit the startup program command as follow if running ros file before calling .bashrc

> gnome-terminal -x bash -c '~/catkin_ws/boot.sh'

> gnome-terminal -- setsid /usr/lib/vino/vino-server

***
# python pip

> sudo apt-get install synaptic

install armadillo and vlc in synaptic

> sudo apt-get install python-pip

> sudo pip install python-vlc

> sudo -H pip2 install --upgrade pip

> sudo pip install GitPython

> python -m pip install --user spicy

> sudo pip install future

> sudo pip install Shapely

alternative use python2.7 or python3 if necessary

> sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1

***
# upgrade linux kernel

download [intel nuc wifi](https://www.intel.com/content/www/us/en/support/articles/000005511/network-and-i-o/wireless-networking.html)

select [linux kernel version](http://kernel.ubuntu.com/~kernel-ppa/mainline/)

download headers, headers-generic, and image deb

> uname -sr

> sudo dpkg -i *.deb

> sudo reboot

> uname -sr

> sudo cp -i iwlwifi-*.ucode /lib/firmware

> sudo update-grub

> sudo reboot

***
# change hostname

> sudo vi /etc/hostname

    [hostname]

> sudo vi /etc/hosts

    127.0.0.1       localhost
    127.0.1.1       [hostname]

***
# disk cleanup

> sudo apt-get clean

> journalctl --vacuum-size=10M

```
#!/bin/bash
# Removes old revisions of snaps
# CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        snap remove "$snapname" --revision="$revision"
    done
```

remove drag_and drop and set bit zero if vmware
> rm -rf ~/.cache/vmware/drag_and_drop

> sudo su -c 'dd if=/dev/zero of=/zero bs=512; rm -v /zero'

do compress in vmware settings

***
# systemback iso

add apt-repository as ubuntu 14.04/16.04
> sudo add-apt-repository ppa:nemh/systemback

add apt-repository as ubuntu 18.04 or later
> sudo add-apt-repository "deb http://ppa.launchpad.net/nemh/systemback/ubuntu xenial main"

> sudo apt-get update && sudo apt-get install systemback unionfs-fuse

revise grub file in order to alter display resolution if necessary

> sudo sed -i -e '/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"/ c GRUB_CMDLINE_LINUX_DEFAULT="quiet splash i915.alpha_support=1"' /etc/default/grub

> sudo update-grub

copy EFI/BOOT from origin iso to custom iso in boot disk

***
# ubuntu boot failure

> unane -r

> modprobe nls iso8859-1

> depmod

> modprobe nls so8859-1

> reboot

***
# gitlab

> sudo apt-get install curl openssh-server ca-certificates postfix

press the Tab key to go to the "OK" button, and then press Enter

> curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh |sudo bash

> sudo apt-get install gitlab-ce

open the file/etc/gitlab/gitlab.rb, and at approximately line 32, change the domain name for external access

> sudo gitlab-ctl stop
> sudo gitlab-ctl reconfigure
> sudo gitlab-ctl restart
> sudo gitlab-ctl start

user: root
passwd: /etc/gitlab/initial_root_password

***
# docker

> sudo apt-get install ca-certificates curl

> sudo install -m 0755 -d /etc/apt/keyrings

> sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

> sudo chmod a+r /etc/apt/keyrings/docker.asc

> echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

> sudo apt-get update

> sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

> sudo docker run hello-world

> sudo docker pull  shreyasgokhale/gc-ros-gazebo-vnc

> sudo docker run -v /home/ouiyeah/bag:/home/ros/Desktop -p 6901:6901 shreyasgokhale/gc-ros-gazebo-vnc

