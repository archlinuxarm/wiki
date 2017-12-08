## Xorg

* The Mali T860MP4 GPU is not supported at this time.  Therefore, no particular Xorg drivers are needed.  The built in modesetting driver will correctly detect the display.
* xf86-input-libinput is preferred over evdev and synaptics for the best experience with the touchpad and touchscreen.

## Audio
Use `alsaucm` (part of the alsa-utils package) to set up the sound card:

```
ALSA_CONFIG_UCM=/opt/alsa/ucm alsaucm -c rk3399-gru-sound set _verb HiFi
```

Note that the port will display as "Headphones" despite using the speakers.  When paired with pulseaudio and pulseaudio-alsa, be aware that the full volume range is addressed through levels 0-9 of the Master volume control.  Levels 10-100 provide no additional gain.

## Backlight control
Add this udev rule at `/etc/udev/rules.d/backlight.rules`, and add your user to the `video` group.

```
SUBSYSTEM=="backlight",RUN+="/usr/bin/chgrp video /sys/class/backlight/%k/brightness /sys/class/backlight/%k/bl_power",RUN+="/usr/bin/chmod 664 /sys/class/backlight/%k/brightness /sys/class/backlight/%k/bl_power"
```

This will allow you to change the backlight brightness by writing values from 0 to 65535 to `/sys/class/backlight/backlight/brightness`.
