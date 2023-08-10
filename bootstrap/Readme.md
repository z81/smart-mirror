## Tty screen rotate settings
armbian-config or /boot/armbianEnv.txt
```
extraargs=fbcon=rotate:3
```
## chromium kiosk mode + xserver
```
sudo apt-get update && apt-get upgrade
sudo apt-get autoremove --purge
sudo apt-get install chromium matchbox-window-manager xserver-xorg x11-xserver-utils unclutter xinit
```
~/mirror/kiosk.sh
```
#!/bin/bash  
# disable DPMS (Energy Star) features. 
xset -dpms  
# disable screen saver 
xset s off  
# don't blank the video device 
xset s noblank  
# disable mouse pointer 
unclutter &  
# run window manager 
matchbox-window-manager -use_cursor no -use_titlebar no  &  
xrandr --output HDMI-1 --rotate left

# xrandr -q
# run browser (change url in command)
# for local files use file:/// instead of http://
/usr/bin/chromium --no-sandbox --window-size=1080,1920 --start-fullscreen --kiosk --icognito --noerrdialogs --disable-tranlate --no-first-run --fast --fast-start --disable-infobars --disable-features=TranslateUI --disk-cache-dir=/dev/null --password-store=basic --autoplay-policy=no-user-gesture-required file://main.html
```
~/.xinitrc
```
/root/mirror/kiosk.sh
```
test x
```
startx
```
## Fix HDMI audio
~/.asoundrc
```
pcm.!default {
        type hw
        card "hdmisound"
}

ctl.!default {
        type hw
        card 0
}
```
restart service
```
systemctl --user restart pulseaudio
```
test audio
```
speaker-test -t wav -c 2
```
## autostart xserver
/etc/systemd/system/getty@tty1.service.d/autologin.conf
```
[Service]
ExecStart=
ExecStart=-/sbin/agetty -o '-p -f -- \\u' --noclear --autologin root %I $TERM
```
~/.bashrc
```
if [ -z "${SSH_TTY}" ]; then   
    startx 
fi
```