## Xorg

* The Mali T860MP4 GPU is not supported at this time.  Therefore, no particular Xorg drivers are needed.  The built in modesetting driver will correctly detect the display.
* xf86-input-libinput is preferred over evdev and synaptics for the best experience with the touchpad and touchscreen.

## Audio
Use `alsaucm` (part of the alsa-utils package) to set up the sound card:

```
ALSA_CONFIG_UCM=/opt/alsa/ucm alsaucm -c rk3399-gru-sound set _verb HiFi
```

Note that the port will display as "Headphones" despite using the speakers.  When paired with pulseaudio and pulseaudio-alsa, be aware that the full volume range is addressed through levels 0-9 of the Master volume control.  Levels 10-100 provide no additional gain.

If using pulseaudio, the following can be placed in your `~/.config/pulse/default.pa` file to create a loopback device that fixes the volume levels, so that 0-100% in a desktop environment's volume bar will correspond to the audio level heard in the spaker.

```
.include /etc/pulse/default.pa
  
load-module module-null-sink sink_name=corrected_speakers sink_properties=device.description=corrected_speakers

load-module module-loopback source=corrected_speakers.monitor sink=alsa_output.platform-sound.stereo-fallback remix=false

set-sink-volume alsa_output.platform-sound.stereo-fallback 6553

set-default-sink corrected_speakers
```
