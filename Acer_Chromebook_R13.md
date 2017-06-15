## Xorg

* The PowerVR GX6250 GPU is not supported at this time.  Therefore, no particular Xorg drivers are needed.  The built in modesetting driver will correctly detect the display.
* xf86-input-libinput is preferred over evdev and synaptics for the best experience with the touchpad and touchscreen.  Touch controls on the touchscreen only align when the screen is in laptop orientation.

## Audio
The following channels must be unmuted for the speakers to work.  The alsamixer utility (part of alsa-utils) can be used to do this by navigating with the arrow keys and pressing **m** to unmute the channel.  If pulseaudio or similar audio mixers are installed, you must use the F6 key when in alsamixer to switch from pulseaudio to the sound device.

* Speaker Channel
* DAC MIXL DAC L2
* DAC MIXL DAC R2
* DAC MIXL Sto DAC Mix L
* DAC MIXR DAC L2
* DAC MIXR DAC R2
* DAC MIXR Sto DAC Mix R
* DAC1 MIXL DAC1
* DAC1 MIXL Stereo ADC
* DAC1 MIXR DAC1
* DAC1 MIXR Stereo ADC
* 003 I05
* 004 I06
* SPK MIXL DAC L1
* SPK MIXR DAC R1
* SPKVOL L
* SPKVOL R
* SPOL MIX DAC L1
* SPOL MIX DAC R1
* SPOL MIX SPKVOL L
* SPOL MIX SPKVOL R
* SPOR MIX DAC R1
* SPOR MIX SPKVOL R

Audio via amixer (also included in the alsa-utils package) may automate this a bit.  The channels above can be used via number id (cset numid).  The numbers corresponding to the above channels are (in order): 1 89 90 88 87 86 85 110 109 108 107 145 144 81 77 68 67 54 53 56 55 51 52

A simple commandline to do this (in BASH or related shells) is:
* for i in 1 89 90 88 87 86 85 110 109 108 107 145 144 81 77 68 67 54 53 56 55 51 52; do amixer -c 0 cset numid=$i on; done

You may also want to decrease the DAC1 (cset numid=10) gain value, otherwise audio will still be fairly loud at the lowest setting.

Headphones:
* HPOVOL L Switch
* HPOVOL R Switch
* HPOVOL MIXL DAC1 Switch
* HPOVOL MIXR DAC1 Switch
* HPO MIX DAC1 Switch

A command for enabling headphones as above is:
* for i in 58 57 63 59 49; do amixer -c 0 cset numid=$i on; done

Muting/Unmuting:

Mute Headphones
* amixer cset numid=4 off 

Unmute Headphones
* amixer cset numid=4 on

Mute Speakers
* amixer cset numid=25 off

Unmute Speakers
* amixer cset numid=25 on


