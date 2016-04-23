# SIRPRANCELOT ARCH LINUX RASPBERRY PI VIDEO RECORDER SETUP GUIDE
*The purpose of this guide is to setup your pi so you can perform recording tasks as outlined by NapoleonWils0n's master git.  Please refer to that for further information on how to actually use.*  

*You do not need to follow "HOW TO COMPILE FFMPEG FOR PI.md" if you run through this guide. "HOW TO COMPILE FFMPEG FOR PI.md" is purely there for people who DON'T want to install the full setup.  I highly recommend though completing this guide.*

## Important to know


**IMPORTANT**, This guide is based on my own experience setting up my pi3.  One important comment about hardware before continuing.  Not all micro SD cards are equal.  I originally used a samsung 64gb class 10 sd card.  Performance was bad.  Kodi ran with big lags.  Installation was slower etc etc.  I didn't know it at the time but you need to track down a GOOD sd card.  You need one that can handle quick transfer of small files.  Either google or I highly recommend samsung evo pro plus (I got a 32gb).  About 10£ odd.  Works a treat.  I've heard the ones that ship with official pi starter kits are fine.. maybe not as good as Samsung one I have but not as bad as the awful Kingston one I have.  ALSO make sure you use a proper 5v charger with pi3 since they can't handle underpowered chargers.  Additionally I made the mistake of buying a case that makes pi3 too hot when doing things like compile.  I subsequently bought FLIRC case which has a built in heatsync.  You'll need somethinn that can handle the heat if you own a pi3 like me.  These things run hot!  You have been warned.  Watch out for these things!!!

Here are links to what I bought:

* Charger https://www.amazon.co.uk/gp/product/B01CCR5P8U/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1
* Samsung 32gb Evo Pro Plus https://www.amazon.co.uk/gp/product/B00WIMC5C4/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1  
* FLIRC case https://thepihut.com/products/flirc-raspberry-pi-3-b-case

## Let's begin!

First Install arch linux using PINN-lite https://sourceforge.net/projects/pinn/
You must have ethernet connected on boot else arch linux does not show in list.. also you maybe need to reboot a few times for it to show up as it is a little flaky
Once downloaded, extract files to a folder and simply copy across files to fat formatted SD (done via diskutility on mac)

There are alternative ways of installing arch linux but this is the easiest if you don't have a linux computer.  Running in VMware or such virtualisation tools will struggle due to sd cards not being picked up so avoid virtualisation like the plague.  Either make on linux system or use PINN-lite.

Once Pinn-lite files copied to SD, safely remove sd card and pop into Pi.  When booting up simply select the arch linux option, let it download and install.  Then upon reboot you're in!

###on pi:

- login with: root / root
- change root password: 
```
passwd
```
- Install sudo:
```
pacman -S sudo
```
- Put in the following:
```
EDITOR=nano visudo
```
- Find User privilege specification and uncomment (remove the #) the line below to say :
```
%wheel ALL=(ALL) ALL
```
- Locate the lines that are currently commented out as follows "## Uncomment to allow members of group sudo to execute any command" to say:
```
%sudo ALL=(ALL) NOPASSWD: ALL
```
- Save and Exit (using CTL+X, Y, Enter)
- Now create a user:
```
useradd -m -g users -G audio,lp,optical,wheel,storage,power,video -s /bin/bash yourusername
```
- Set user password
```
passwd yourusername
```
- logout
```
logout
```
- Test your new login details by sigining in

###Switch to laptop/PC/mac (you don't have to but its a lot easier/faster for some of next steps)

- Find IP from Router then switch to mac terminal for next bit.  Type (replacing IP below):
```
ssh yourusername@192.168.2.232
```
- Next we want to allow root access over ssh
```
sudo nano /etc/ssh/sshd_config
```
- change the line beginning #PermitRootLogin to:
```
PermitRootLogin yes
```
- Save (ctrl+x, y, enter)
- reboot
```
sudo reboot
```
- Now you can ssh using root by (Replace IP with your own:
```
ssh root@192.168.2.1
```
- Switch back to using yourusername for rest of guide unless otherwise told to switch to root
- One final step we need to do is edit Pacman 
```
sudo nano /etc/pacman.conf
```
- remove "#" from "#RootDir = /" 
```
RootDir = /
```
- save (ctrl+x, y, enter)

###Switch back to pi

- Enter
```
sudo rm /etc/localtime
```

###Switch back to laptop/pc/mac OR stay on pi

- Check time 
```
timedatectl
 ```
 - set correct timezone - refer to https://hreikin.wordpress.com/2014/04/27/arch-linux-raspberry-pi-install-guide-updated/ to find timezone
```
sudo timedatectl set-timezone Europe/London
```
- recheck time is now changed
```
timedatectl
```
- create symlink
 ```
sudo ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime
```
- Setup ntp sync
```
timedatectl set-ntp true
```
- Setup keyboard: 
```
sudo nano /etc/vconsole.conf
```
- enter this line if UK if not refer to arch linux website - https://wiki.archlinux.org/index.php/Keyboard_configuration_in_console: 
```
KEYMAP=uk
```

###Switch back to pi

- Change Pi name from Alarmpi to for e.g. pi (edit the name you see listed by deleting for i.e. Alarm part of Alarmpi)
```
sudo nano /etc/hostname 
```

###Switch back to laptop/pc/mac OR stay on pi

- Add new Pi name to /etc/hosts
```
sudo nano /etc/hosts
```
i.e.
```
#
# /etc/hosts: static lookup table for host names
#

#<ip-address>   <hostname.domain.org>   <hostname>
127.0.0.1       localhost.localdomain   localhost    pi
::1             localhost.localdomain   localhost    pi

# End of file
```
- Create Pacman Key (Do this on both Pi directly AND laptop if you want to follow me... pacman init keys are important else things will not install properly).. if not doing with ssh and Pi refer to https://hreikin.wordpress.com/2014/04/27/arch-linux-raspberry-pi-install-guide-updated/ NB pacman-key --init has 2x "-"
```
sudo pacman-key –-init
```
- In another window on laptop/pc/mac OR if you can create another window on Pi (my usb remote keyboard does not have FN keys to open new terminal window so I had to use combination).. do this a few times to play safe.. trying to sync the 
```
ls -R / && ls -R / && ls -R /
```
Update system and packages (CHANGE MIRRORS comment out main mirror and choose manually i.e. france and netherlands using: sudo nano /etc/pacman.d/mirrorlist => comment out => #Server = http://mirror.archlinuxarm.org/$arch/$repo => and remove # from i.e. france and netherlands servers => save => I found these servers much quicker)
```
sudo pacman -Syu
```
- reboot system
```
sudo reboot
```

###Install Core Files & Desktop Environment

- Install Core files as well as desktop environment
```
sudo pacman -S alsa-utils alsa-firmware alsa-lib alsa-plugins xf86-video-fbdev
```
- Press enter, enter, y to get defaults

- To install desktop environment use this (optional but highly recommended)
```
sudo pacman -S mate mate-extra 
```
*NB I like having a desktop environment on tap - it means you can load it up as and when you need it to do more plus it handles usb devices very well etc etc.*

###Setup .Xinitrc
#####This section is only needed if you want Desktop Environment

- Install Xorg
```
sudo pacman -S xorg xorg-xinit xorg-server xorg-server-utils xterm xorg-twm xorg-xclock xorg-xrefresh xorg-xset
```
- Create .xinitrc file
```
sudo nano .xinitrc
```
- Enter exactly this:
```
#!/bin/sh
#
# ~/.xinitrc
#
# Executed by startx (run your window manager from here)

if [ -d /etc/X11/xinit/xinitrc.d ]; then
  for f in /etc/X11/xinit/xinitrc.d/*; do
    [ -x "$f" ] && . "$f"
  done
  unset f
fi

# exec enlightenment_start
# exec i3
exec mate-session
# exec xmonad
# exec startlxqt
# exec startlxde
# exec awesome
# exec bspwm
# exec gnome-session
# exec gnome-session --session=gnome-classic
# exec startkde
# exec startxfce4
# exec startfluxbox
# exec openbox-session
# exec cinnamon-session
# exec pekwm
# exec catwm
# exec dwm
# exec startede
# exec icewm-session
# exec jwm
# exec monsterwm
# exec notion
# exec startdde       # deepin-session
```
- Save it (Ctl+x, y, enter)
- check for .xinitrc is present: 
```
ls -la
```

###Desktop Environment
#####This section is only needed if you want Desktop Environment

- You can launch the desktop environemnt and configure to your liking.  To launch simply type (NB you MUST be using your user account NOT root):
```
startx
```
- Recommended configurations:

System > Preferences > Look & Feel > Theme: Blackmate / Background: choose one to your liking

System > Preferences > Look & Feel > Screensaver > Choose one of your liking

Applications > Sound & Video > Drag Kodi icon to desktop

- To exit back to command line on menu goto: 

System > Logout yourusername > Logout

##KODI SETUP

- Install kodi
```
sudo pacman -S kodi-rbp
```
- Change GPU settings on pi
```
sudo nano /boot/config.txt
```
Change line: 
```
change gpu_mem=256 (or 128)
```
- EITHER: If you didn't skip destop GUI installation
```
sudo pacman -S omxplayer-git
```
- OR: if you skipped installing desktop environment
```
sudo pacman -S omxplayer-git xorg xorg-xinit xorg-server xorg-server-utils xterm xorg-twm xorg-xclock xorg-xrefresh xorg-xset
```
- Edit Raspberry Rules:
```
sudo nano /etc/udev/rules.d/raspberrypi.rules
```
- Add following rule:
```
SUBSYSTEM=="tty", KERNEL=="tty0", GROUP="tty", MODE="0666"
```
- Save (ctl+x, y, enter)

- Logout of your user and login as root

- add the following udev rule by copying this command into terminal:
```
echo 'SUBSYSTEM=="vchiq",GROUP="video",MODE="0660"' > /etc/udev/rules.d/10-vchiq-permissions.rules
```
- Logout of your root and resume as user

- Create the following script (allows Kodi to exit else you always get a black screen and have to reboot - v annoying!):
```
sudo nano /usr/local/bin/startkodi
```
- Enter exactly:
```
#!/bin/bash
fbset_bin=`which fbset`
xset_bin=`which xset`
xrefresh_bin=`which xrefresh`
if [ ! -z $fbset_bin ]; then
  DEPTH2=`$fbset_bin | head -3 | tail -1 | cut -d " " -f 10`
fi
kodi "$@"
if [ ! -z $fbset_bin ]; then
  if [ "$DEPTH2" == "8" ]; then
    DEPTH1=16
  else
    DEPTH1=8
  fi
  $fbset_bin -depth $DEPTH1 > /dev/null 2>&1
  $fbset_bin -depth $DEPTH2 > /dev/null 2>&1
fi
if [ ! -z $xset_bin ] && [ ! -z $xrefresh_bin ]; then
  if [ -z $DISPLAY ]; then
    DISPLAY=":0"
  fi

  $xset_bin -display $DISPLAY -q > /dev/null 2>&1
    if [ "$?" == "0" ]; then
      $xrefresh_bin -display $DISPLAY > /dev/null 2>&1
    fi
fi
VT="$(fgconsole)"
if [ "$VT" ]; then
  sudo chvt 7
  sudo chvt "$VT"
fi
```
- Give script permissions to be executable
```
sudo chmod a+x /usr/local/bin/startkodi
```
- You can now start kodi with (don't worry about any error messages): 
```
startkodi
```
- To fix the exit problem when launching Kodi from desktop environment do as follows: 
```
sudo nano /usr/share/applications/kodi.desktop
```
-then change to:
```
[Desktop Entry]
Version=1.0
Name=Kodi media center
GenericName=Media center
Comment=Manage and view your media
Exec=startkodi
Icon=kodi
Terminal=false
Type=Application
Categories=AudioVideo;Video;Player;TV;

Actions=Fullscreen;Standalone;

[Desktop Action Fullscreen]
Name=Open in fullscreen
Exec=startkodi -fs
OnlyShowIn=Unity;

[Desktop Action Standalone]
Name=Open in standalone mode
Exec=kodi --standalone
OnlyShowIn=Unity;
```
- Save: Ctrl+x, y, enter

- To launch Kodi from boot edit/create:
```
sudo nano /etc/systemd/system/kodi.service
```
- Change to the following:
```
[Unit]
Description = Starts an instance of Kodi
After = remote-fs.target

[Service]
User = yourusername
Group = wheel
Type = simple
ExecStart = /usr/local/bin/startkodi -l /run/lirc/lircd

[Install]
WantedBy = multi-user.target
```
- test it's working properly:
```
systemctl start kodi
```
- If all is good let's enable it for boot:
```
systemctl enable kodi
```
- Reboot and you're done
  

###SETUP WIFI

- Wifi: to generate profile (must do as root): 
```
wifi-menu -o
```
- Wifi profile is stored in /etc/netctl: e.g. mine is wlan0-HappyHippo
- to view wifi file name so you can boot from start:
```
cd /etc/netctl
ls
```
- then view what the file is in there.. prob wlan0-yournetworkname
- to enable wifi from boot i.e.: 
```
netctl enable wlan0-yournetworkname
```

###SOUND

- Edit:
```
sudo nano /boot/config.txt
```
- Add 2 lines to config.txt, save, then reboot: 
```
dtparam=audio=on
amixer cset numid=3 2
```
- Reboot

##NAPOLOEN WILSON RECORDING

- install tools:
```
sudo pacman -S base-devel bash-completion
```
- press enter for default all and then y to proceed

###Create Builds Directory
```
mkdir -p ~/builds
```
### change directory to the ~/builds directory
```
cd ~/builds
```
###Download Package Query
```
curl -O https://aur.archlinux.org/cgit/aur.git/snapshot/package-query.tar.gz
```
###Untar the file
```
tar zxvf package-query.tar.gz
```
###Enter folder containing PKBUILD
```
cd package-query
```
###Install the package
```
makepkg -si
```
###Install Yaourt
- change directory before reinstalling yaourt
```
cd ~/builds
```
- reinstall yaourt: download yaourt
```
curl -O https://aur.archlinux.org/cgit/aur.git/snapshot/yaourt.tar.gz
```
- untar the file
```
tar zxvf yaourt.tar.gz
```
- Enter the folder containing the PKGBUILD
```
cd yaourt
```
- install the package
```
makepkg -si
```
###Update
- update pacman
```
sudo pacman -Syu
```
- update yaourt
```
yaourt -Syua
```
###Finish off a few more procedures
- copy and rename /usr/share/gnupg/gpg-conf.skel => create folder /home/yourusername/.gnupg and rename gpg-conf.skel as gpg.conf
```
mkdir -p /home/yourusername/.gnupg
cp /usr/share/gnupg/gpg-conf.skel /home/yourusername/.gnupg/gpg-conf.skel
mv gpg-conf.skel gpg.conf
```
- edit the folow:
```
sudo nano ~/.gnupg/gpg.conf
```
- uncomment (remove # from) the following line:
```
keyserver-options auto-key-retrieve
```
- save (ctrl+x, y, enter)

###install gcc-libs
```
sudo pacman -S gcc-libs
```
### Install rtmpdump youtube aria2 mplayer mpv
```
sudo pacman -S git rtmpdump youtube-dl aria2 mplayer mpv
```
### Install imagemagick nodejs
```
sudo pacman -S nodejs git redis imagemagick
```
### install vlc which will install an old version of ffmpeg which we can disregard and ignore
```
sudo pacman -S vlc libdvdcss 
```
- choose defaults by pressing enter, enter, Y

##  COMPILE A NEW FULL FFMPEG
#####You've already installed most of what is needed so we just need to add a few more files

- Install Snappy & Wavpack
```
sudo pacman -S snappy wavpack
```
- Then in terminal and move to home directory:
```
cd ~
```
- Then grab a copy of ffmpeg on github (Will download to home folder)
```
sudo git clone git://source.ffmpeg.org/ffmpeg.git ffmpeg
```
- Then move into ffmpeg directory
```
cd ffmpeg
```
- Then configure FFmpeg (should output no errors by end... make sure to install any missing ones with pacman)
```
./configure --enable-avisynth --enable-fontconfig --enable-gpl --enable-libass  --enable-libfreetype --enable-libgsm --enable-libmodplug --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopus --enable-libschroedinger --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libtheora --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libvpx --enable-libx264 --enable-libx265 --enable-libxvid --enable-version3 bindir="/usr/local/bin"
```
*The compile will take upto 1.5hours... though if done with quad core option maybe as low as 30mins.  So go have coffee.. don't worry about minor warnings but do make sure it completes without a fail message at end.. if does... repeat.. ALSO i recommend removing pi from casing for compiling as it will run hot... if have heatsink might be alright but in my case I had to remove since it was overheating when I ran the compile multiple times during my initial experimentation (Red square in top right of screen) but after taking out of case I had a yellow square in top corner which was okay... just means it's running hot hot hot!)*

- To compile choose ONE of the following: first one is for non quad-core pi, second one (j4) is for quadcore (pi3) and it runs much  faster!!):
```
sudo make

OR
`
sudo make -j4 
```
- When compile successfully completes without error (ignore warnings) install the compiled files!
```
sudo make install
```
- Test everything installed okay (should find ffmpeg by following command and it will list details about it which will match the configuration we did above)
```
ffmpeg -version
```
- Done!

- Try it out with the following command:
```
ffmpeg -y -threads 0 -i enteryoururlhere -map 0 -acodec copy -vcodec copy /home/yourusername/Desktop/test.mkv 
```
###Scheduling with AT command

- Install At Command and enable it from boot
```
sudo pacman -S at
sudo systemctl start atd
sudo systemctl enable atd
```
- examples
```
at now + 1min (enter) OR at 10:48 AM (enter) or  at 3:25 PM 4/18/2016 (enter)
rip-record /home/yourusername/Desktop/url.txt -t 00:10:30 (enter)
(CTRL+D)
```
- atq to check queue
- pgrep ffmpeg (to find process id)
- kill 1593 (replace number with pgrep one)

###VSFTPD (FTP CLIENT) - I recommend Filezilla to use with this

- Install VSFTPD
```
sudo pacman -S vsftpd
```
- Change /etc/sudoers 
```
sudo nano /etc/sudoers
```
- Add line at end:
```
yourusername ALL=NOPASSWD: /usr/bin/chvt
```
- save: ctrl+x, y, enter

- Edit /etc/vsftpd.conf
```
sudo nano /etc/vsftpd.conf
```
- to (add local-umask under write_enable as a new line and make sure to remove the # for lines below):
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=0002
file_open_mode=0777
anon_upload_enable=YES
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
ftpd_banner=Welcome to blah FTP service.
listen=YES
```
- Save (ctrl+x, y, enter)

- start the ftp service:
```
sudo systemctl start vsftpd.service
```
- Enable ftp from boot:
```
sudo systemctl enable vsftpd.service
```
- Login with filezilla or other ftp client using either root/password or yourusername/password using IP address of raspberry pi

i.e. 192.168.2.1 user=root pass=yourpassword

###SAMBA

- Install the package with pacman.
```
sudo pacman -S samba
```
- Copy the example configuration file.
```
sudo cp /etc/samba/smb.conf.default /etc/samba/smb.conf  
```
- Create an account with the same username as the Linux username.
```
sudo pdbedit -a -u yourusername
```
- Enter the password when prompted. This will be used to login when browsing shares. It does not have to be the same as the Linux user’s password.

- Change the password at any time if needed by
```
sudo smbpasswd yourusername
 ```
- Restart the service to apply new changes.
```
sudo systemctl restart smbd nmbd
 ```
- Run Samba
```
sudo systemctl start smbd nmbd
 ```
- Enable the service to run on boot with systemd.
```
sudo systemctl enable smbd nmbd
```
#### mounting a smb share

- install smbclient
```
sudo pacman -S smbclient
```
- create a mount point
```
mkdir -p /home/yourusername/mount
```
- mount smb share as read write
```
sudo mount -t cifs //192.168.1.25/video/Downloads/ffmpeg /home/yourusername/mount -o user=username,workgroup=workgroup,rw
```
- Check contents
```
ls mount/
```
- To add smb share on boot
```
sudo nano /etc/fstab
```
- then add:
```
//192.168.2.1/Share /home/yourusername/mount cifs username=yourusernameforshareddevice,password=enteryourpass 0 0
```
Then save... job done!

#### unmounting smb share
```
sudo unmount /home/username/mount
```
###TIME SYNC IN KODI

- For XBMC go into appearance > international > set timezone!!!!

###MOUNTING A USB DEVICE

- either boot up into Desktop Environment or from headless mode isse these two commands:
```
ls -l /dev/disk/by-uuid/ (find uuid of device norm sda1 etc)
sudo mkdir /mnt/usbdevice (make a directory to mount from)
```
- To have it load up from boot:
```
sudo nano /etc/fstab (Add line below replacing folder and UUID)
```
- Then add
```
UUID=Yours /mnt/usbdevice vfat user,noauto,noatime,flush 0 0
```
- to mount usb device:
```
mount /mnt/usbdevice
```




