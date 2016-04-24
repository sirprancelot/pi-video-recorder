##  COMPILE A NEW FULL FFMPEG FOR ARCH LINUX RASPBERRY PI

- Edit Pacman
```
sudo nano /etc/pacman.conf
```
- remove "#" from "#RootDir = /" 
```
RootDir = /
```
- install tools required
```
sudo pacman -S base-devel bash-completion
```
- install git
```
sudo pacman -S git
```
- install vlc (contains most of items you need for configuration and even ffmpeg but you can disregard the VLC copy of ffmpeg)
```
sudo pacman -S vlc
```
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
- All your compiled files (ffmpeg, ffprobe, ffplay, ffserver) can now be found in:
```
/usr/local/bin
```
- Try it out with the following command:
```
ffmpeg -y -threads 0 -i enteryoururlhere -map 0 -acodec copy -vcodec copy /home/yourusername/Desktop/test.mkv 
```
- Give yourself a pat on the back!
