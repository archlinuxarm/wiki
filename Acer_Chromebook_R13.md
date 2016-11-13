## Xorg

* The PowerVR GX6250 GPU is not supported at this time.  Therefore, no particular Xorg drivers are needed.  The built in modesetting driver will correctly detect the display.
* xf86-input-libinput is preferred over evdev and synaptics for the best experience with the touchpad and touchscreen.

## Audio
The following channels must be unmuted for the speakers to work.  The alsamixer utility (part of alsa-utils) can be used to do this by navigating with the arrow keys and pressing **m** to unmute the channel.

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

You may also want to decrease the DAC1 gain value, otherwise audio will still be fairly loud at the lowest setting.
