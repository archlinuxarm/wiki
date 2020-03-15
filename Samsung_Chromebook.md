## Better touchpad responsiveness

Create the file /etc/X11/xorg.conf.d/50-touchpad.conf and add the following to it:

```
Section "InputClass"
        Identifier              "touchpad"
        MatchIsTouchpad         "on"
        Option                  "FingerHigh"    "5"
        Option                  "FingerLow"     "5"
EndSection
```

## Enable audio

1. Install alsa-utils: `pacman -S alsa-utils`
2. Run alsamixer as root.
3. Select correct sound card by pressing F6 and selecting '0  Snow-I2S-MAX98095'
4. Arrow right until you see four items starting with Left Speaker Mixer, and press M on all four channels to un-mute them.
5. Arrow right some more until you find four more starting with Right Speaker Mixer and un-mute them as well.
6. Arrow all the way back left to the Speaker channel and raise the volume a little. Keep it low (< 50ish), since alsa will let you get dangerously high for these speakers.

Alternatively, the following commands can be issued per channel to amixer. Requires alsa-utils as well.

```
amixer -c 0 set 'Right Speaker Mixer Right DAC1' unmute 
amixer -c 0 set 'Right Speaker Mixer Mono DAC3' unmute
amixer -c 0 set 'Right Speaker Mixer Mono DAC2' unmute
amixer -c 0 set 'Right Speaker Mixer Left DAC1' unmute
amixer -c 0 set 'Left Speaker Mixer Right DAC1' unmute
amixer -c 0 set 'Left Speaker Mixer Mono DAC3' unmute
amixer -c 0 set 'Left Speaker Mixer Mono DAC2' unmute
amixer -c 0 set 'Left Speaker Mixer Left DAC1' unmute
```

## Suspend when closing the lid

Install acpid and pm-utils: `pacman -S acpid pm-utils`

Edit /etc/acpi/handler.sh and near the bottom you'll see the button/lid section. Add pm-suspend to the close section to look like:

```
button/lid)
    case "$3" in
        close)
            logger 'LID closed'
            pm-suspend
            ;;
```

Enable and start acpid:

```
systemctl enable acpid
systemctl start acpid
```

Closing the lid will now trigger a suspend, and opening it will wake the system.

## User permissions for backlight control

To allow all users to modify the backlight control at */sys/class/backlight/backlight/brightness*, create and edit the file */etc/tmpfiles.d/brightness.conf* with these contents:

```
f /sys/class/backlight/backlight/brightness 0666 - - - 800
```

On boot, systemd will now set the permissions of that control file to be world writable. Acceptable values are 0-7.
