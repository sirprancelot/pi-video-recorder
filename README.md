# SIRPRANCELOT ARCH LINUX RASPBERRY PI VIDEO RECORDER SETUP GUIDE
*The purpose of this guide is to setup your pi so you can perform recording tasks as outlined by NapoleonWils0n's master git.  Please refer to that for further information on how to actually use.*  

*You do not need to follow "HOW TO COMPILE FFMPEG FOR PI.md" if you run through this guide. "HOW TO COMPILE FFMPEG FOR PI.md" is purely there for people who DON'T want to install the full setup.  I highly recommend though completing this guide.*

## Important to know


**IMPORTANT**, This guide is based on my own experience setting up my pi3.  One important comment about hardware before continuing.  Not all micro SD cards are equal.  I originally used a samsung 64gb class 10 sd card.  Performance was bad.  Kodi ran with big lags.  Installation was slower etc etc.  I didn't know it at the time but you need to track down a GOOD sd card.  You need one that can handle quick transfer of small files.  Either google or I highly recommend samsung evo pro plus (I got a 32gb).  About 10£ odd.  Works a treat.  I've heard the ones that ship with official pi starter kits are fine.. maybe not as good as Samsung one I have but not as bad as the awful Kingston one I have.  ALSO make sure you use a proper 5v charger with pi3 since they can't handle underpowered chargers.  Additionally I made the mistake of buying a case that makes pi3 too hot when doing things like compile.  I subsequently bought FLIRC case which has a built in heatsync.  You'll need somethinn that can handle the heat if you own a pi3 like me.  These things run hot!  You have been warned.  Watch out for these things!!!

Here are links to what I bought:

* Charger https://www.amazon.co.uk/gp/product/B01CCR5P8U/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1
* Samsung 32gb Evo Pro Plus https://www.amazon.co.uk/gp/product/B00WIMC5C4/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1  
* FLIRC case https://thepihut.com/products/flirc-raspberry-pi-3-b-case
* Rii Remote with keyboard (there's prob better but I had this left over and lying around and it does the job fine) - https://www.amazon.co.uk/i25-Function-Controller-Rechargable-Raspberry/dp/B00INITM1E/ref=sr_1_1?ie=UTF8&qid=1461468936&sr=8-1&keywords=rii+remote

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

###Setup Wifi Raspbery Pi3

- To choose/setup wifi:
```
wifi-menu
```
- To generate a profile (must do as root) to automatically use each time you boot: 
```
wifi-menu -o
```
- Your wifi profile is stored in /etc/netctl: e.g. mine is wlan0-HappyHippo
- to view wifi file name so you can boot from start:
```
cd /etc/netctl
ls
```
- then view what the file is in there.. prob wlan0-yournetworkname
- to enable wifi to always start from boot: 
```
netctl enable wlan0-yournetworkname
```
###Configure the basics

- Install sudo:
```
pacman -S sudo
```
- Update your pi
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
- Test your new login details by signing in

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
- Now you can ssh using root by (Replace IP with your own):
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

- Put in the following AS ROOT USER ON PI (can't be done over SSH)
```
sudo rm /etc/localtime
```

###Switch back to laptop/pc/mac OR stay on pi

- Logout as root and log back in as your username@ipadress
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
- enter this line if you're based in UK, if not refer to arch linux website - https://wiki.archlinux.org/index.php/Keyboard_configuration_in_console: 
```
KEYMAP=uk
```

###Switch back to pi

- Change Pi name from "Alarmpi" to for e.g. "pi" (edit the name you see listed by deleting for i.e. Alarm part of Alarmpi)
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
- Create Pacman Key (Do this on both Pi directly AND laptop if you want to follow me... pacman init keys are important else things will not install properly).. if not doing with ssh and Pi combo then refer to https://hreikin.wordpress.com/2014/04/27/arch-linux-raspberry-pi-install-guide-updated/ NB pacman-key --init has 2x "-"
```
sudo pacman-key –-init
```
- In another window on laptop/pc/mac OR if you can create another terminal on Pi (my usb remote keyboard does not have FN keys to open new terminal window so I had to use my mac as well as pi).. do this a few times to play safe.. what you want to do is trigger the init command and quickly launch the below command which will generate lots of random text to help with key genereation: 
```
ls -R / && ls -R / && ls -R /
```
- Update system and packages 
- CHANGE MIRRORS: comment out main mirror and choose manually i.e. france and netherlands using: 
```
sudo nano /etc/pacman.d/mirrorlist 
```
- comment out main mirror (add # in front of):
```
#Server = http://mirror.archlinuxarm.org/$arch/$repo
```
- and remove # from in front of i.e. france and netherlands servers or whatever is closest.  You may have to trial and error here but I found the main mirror quite slow when compared to selecting other ones in nearby countries
- Update your system:
```
sudo pacman -Syu
```
- reboot system:
```
sudo reboot
```

###Install Core Files & Desktop Environment

- Install Core files
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
- EITHER: If you didn't skip destop environment installation
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

###KODI Settings to be aware of

- If time is off in Kodi you need to goto => appearance => international => set timezone!!!!
- To prevent stuttering in videos you need to goto => System => video => settings level advanced => Playback => set adjust display refresh rate to "always" => this fixes stuttering issues

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

- INstall pulse audio:
```
sudo pacman -S pulseaudio
```
- Unmute mixer and set to 0 DB
```
alsamixer
```
- Check it's working:
```
aplay -l
speaker-test -c 2
```
- Also a fix for mute problem in mate and elsewhere:
```
sudo nano /etc/pulse/default.pa
```
- comment out line "module-default-device-restore":
```
#module-default-device-restore
```
- Reboot after doing this fix

- Check amixer and make sure its not still muted
```
amixer
```
- if still muted reset using alsamixer again setting the levels to 0db

- reboot again

- check amixer
```
amixer
```
*DO NOT USE BELOW IF SOUND IS WORKING BY THS STAGE.  If still not working use either (I did these and can't remember if neccess to fix problem so leave until the v end after rebooting sufficiently before attempting and only use if you're still getting mute issue.. don't use if it's all working by this stage... move onto time sync in Kodi):*
```
amixer set PCM 1+
```
- check amixer unmuted or 
```
amixer set PCM 1+ toggle
```
- check amixer is unmuted or 
```
amixer -D set PCM 1+ toggle
```
- reboot again

- should be fixed.  If in event of mucking up sound.  Uninstall all alsa and pulse except alsa lib.  Re-install again and defaults will be back.

##SETTIING UP PI-VIDEO-RECORDER (NAPOLOEN WILSON RECORDING)

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
- copy and rename /usr/share/gnupg/gpg-conf.skel => create folder /home/yourusername/.gnupg and rename gpg-conf.skel as gpg.conf (REPLACE YOURUSERNAME)
```
mkdir -p /home/yourusername/.gnupg
cp /usr/share/gnupg/gpg-conf.skel /home/yourusername/.gnupg/gpg-conf.skel
cd /home/yourusername/.gnupg/ 
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
if above doesn't work try:
```
sudo git clone https://github.com/FFmpeg/FFmpeg.git ffmpeg
```
- Then move into ffmpeg directory
```
cd ffmpeg
```
- Then configure FFmpeg (should output no errors by end... make sure to install any missing ones with pacman)
```
sudo ./configure --enable-avisynth --enable-fontconfig --enable-gpl --enable-libass  --enable-libfreetype --enable-libgsm --enable-libmodplug --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopus --enable-libschroedinger --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libtheora --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libvpx --enable-libx264 --enable-libx265 --enable-libxvid --enable-version3 bindir="/usr/local/bin"
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
###Setup Pi-Video-Recorder.git

- if not already there make git folder:
```
mkdir -p ~/git
```
- Cd into it
```
cd ~/git
```
- Clone pi-video-recorder
```
git clone https://github.com/sirprancelot/pi-video-recorder.git
```
- copy player config to kodi
```
cp  ~/git/pi-video-recorder/playercorefactory.xml ~/.kodi/userdata/playercorefactory.xml
```
- To pull latest git
```
git pull
```
- setup bash scripts
```
sudo nano ~/.bashrc
```
- Add this to file (change username to yours i.e. sirprancelot):
```
export PATH=${PATH}:/home/sirprancelot/git/pi-video-recorder/Scheduler:/home/sirprancelot/git/pi-video-recorder/bash-scripts
```
- source your ~/.bashrc to pick up the scripts
```
. ~/.bashrc
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

- To be able to see all hidden files in Filezilla goto main menu > Server > Force Showing Hidden Files

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

###MOUNTING A USB DEVICE

- *EASY WAY*:  Boot up into Mate environment- If USB not visible... unplug and replug in to see it connected properly

- For headless mode and so USB will show up in Kodi when autostarted install udiskie:
```
sudo pacman -S udiskie
```
- Next add we need to edit ~/.xinitrc:
```
sudo nano ~/.xinitrc
```
- Add this line at the top above # Executed by startx (run your window manager from here) text
```
udiskie &
```
- Save and exit Nano

- Next we need to add some new rules... create file:
```
sudo nano /etc/polkit-1/rules.d/50-udiskie.rules
```
- Copy in this text and save:
```
polkit.addRule(function(action, subject) {
  var YES = polkit.Result.YES;
  // NOTE: there must be a comma at the end of each line except for the last:
  var permission = {
    // required for udisks1:
    "org.freedesktop.udisks.filesystem-mount": YES,
    "org.freedesktop.udisks.luks-unlock": YES,
    "org.freedesktop.udisks.drive-eject": YES,
    "org.freedesktop.udisks.drive-detach": YES,
    // required for udisks2:
    "org.freedesktop.udisks2.filesystem-mount": YES,
    "org.freedesktop.udisks2.encrypted-unlock": YES,
    "org.freedesktop.udisks2.eject-media": YES,
    "org.freedesktop.udisks2.power-off-drive": YES,
    // required for udisks2 if using udiskie from another seat (e.g. systemd):
    "org.freedesktop.udisks2.filesystem-mount-other-seat": YES,
    "org.freedesktop.udisks2.filesystem-unmount-others": YES,
    "org.freedesktop.udisks2.encrypted-unlock-other-seat": YES,
    "org.freedesktop.udisks2.eject-media-other-seat": YES,
    "org.freedesktop.udisks2.power-off-drive-other-seat": YES
  };
  if (subject.isInGroup("storage")) {
    return permission[action.id];
  }
});
```
- Next we need to create a systemd .service file:
```
sudo nano /etc/systemd/system/udiskie.service
```
- Copy in the following replacing username with your own:
```
[Unit]
Description=handle automounting

[Service]
User=sirprancelot
ExecStart=/usr/bin/udiskie
Restart=always

[Install]
WantedBy = multi-user.target
```
- Save and exit

- Now let's enable this to start on boot
```
sudo systemctl enable udiskie.service
```
- Reboot your device and test it out!  If auto starting into Kodi if your USB device is already plugged in you should be able to see it listed under Video > files in kodi and be able to select the device and view contents.  If you want to view contents in terminal you'll need to find the location which is (replace with your username where appropraite):
```
cd /run/media/sirprancelot/
```
- You should now be able to see contents.  Let's make this a bit easier though and add a Media folder in our home:
```
cd
mkdir Media
```
- Now let's symlink from /run/media/sirprancelot to our home folder (replace username where appropriate):
```
ln -s /run/media/sirprancelot/ /home/sirprancelot/Media/
```
- You shouldn't need to but in case of problems do try to install/reinstall the following:
```
sudo pacman -S polkit
```
- To manually unmount using udiskie simply type:
```
udiskie-umount -a
```
- This will remove all devices.  To mount manually all devices you can do by:
```
udiskie-mount -a
```

### Playing Flash & HTML5 Content in a browser

######INSTALLING CHROMIUM

- Current Chromium via Pacman is not working!!! After much headache I realised this.  The best working version is:
```
http://www.mediafire.com/download/8408b401atf563c/chromium-49.0.2623.112-1-armv7h.pkg.tar.xz
```
- This is from: https://archlinuxarm.org/forum/viewtopic.php?f=60&t=9109&start=40

- To install download this file to your home root and use follwing command:
```
sudo pacman -U chromium-49.0.2623.112-1-armv7h.pkg.tar.xz
```
INSTALLING FLASH

- Download from the following location:

http://dl.free.fr/qVkzvqSiB

This is from: https://ubuntu-mate.community/t/tutorial-flash-player-for-chromium-and-firefox/3598

- Copy across to Pi and Untar the file in the root of your home directory (i.e. /home/sirprancelot/)

tar Jxvf pepper-flash-v20.0.0.228-r1.tar.xz

- Then do the following commands:

cd pepper-flash
sudo mkdir /usr/lib/PepperFlash
sudo cp * /usr/lib/PepperFlash

- Edit/create Chromium Flags file 

sudo nano .config/chromium-flags.conf

- Add useragent and flash

--user-agent="Mozilla/5.0 (X11; Linux i686) AppleWebKit/535.2 (KHTML, like Gecko) Chrome/20.0.1132.47 Safari/536.11"
--user-agent="Chrome/28.0.1500.71"
--user-agent="Mozilla/5.0 (X11; Linux i686) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.75 Safari/537.36"
--ppapi-flash-path=/usr/lib/PepperFlash/libpepflashplayer.so --ppapi-flash-version=20.0.0.228-r1

PROBLEM WITH RESIZE IN MATE

- Not good for Web... only way would be overclocking https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=139138 and here is a thread that discusses how to improve.  I wouldn't personally use VLC, MPV or watch video in browser on Pi.  I have amazon fire stick for all of that PLUS to be honest I would use Kodi over VLC or MPV as it works flawlessly.  For catchup tv not found via Kodi or Amazon, netflix etc... I would just use Amazon Fire Stick or Roku or other.


