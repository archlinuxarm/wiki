## Audio
`alsa-utils` should supply the needed programs to use onboard sound. Default
volume can be adjusted using alsamixer.

A key change with Linux kernel version 4.4.x for ARM related to ALSA and to the
needed sound module: in order to use tools such as alsamixer with the current
kernel, users must modify `/boot/config.txt` to contain the following line:

    dtparam=audio=on

### Caveats for Audio
To force audio over HDMI, add this to `/boot/config.txt`:

    hdmi_drive=2

If you experience distortion using the 3.5mm analogue output:

    audio_pwm_mode=2

## Bluetooth

### firmware
To be able to talk to the bluetooth chip, you must install the services, firmware, and UDEV rules. 
The [pi-bluetooth package](https://aur.archlinux.org/packages/pi-bluetooth/)
from the AUR is available to do this. 

### wifi-coexistence 

The BCM43* series chips are notorious for problems when both wifi and bluetooth are used at the same time. This coexistence issue comes in varying degrees of severity but for most users makes using the Pi in bluetooth A2DP mode while also using wifi impossible. Bluetooth buffer underruns are  caused by sharing the UART device, resulting in skipping, popping, hissing, and generally unusable audio. For some users, this also effects peripherals such as mice and keyboards. A firmware fix was found for both the Pi3 and Pi ZeroW, discussion of this fix can be found [in this github issue](https://github.com/raspberrypi/linux/issues/1402). 

To deploy the fix to the current firmware in Arch add the following lines:

```
btc_mode=1
btc_params8=0x4e20
btc_params1=0x7530
```

to the end of these firmware config files:

`/usr/lib/firmware/updates/brcm/brcmfmac43430-sdio.txt`

`/usr/lib/firmware/updates/brcm/brcmfmac43455-sdio.txt`

This fix can be found in [this git commit](https://github.com/piCorePlayer/firmware-rpi-wireless/commit/7d926a074484e1b43974273a0cdeed9cf3daff9c). The fix is experimental and is not yet deployed to the [upstream package](https://github.com/raspberrypi/firmware)

## Video
The X.org driver for Raspberry Pi can be installed with the `xf86-video-fbdev` 
or `xf86-video-fbturbo-git` package.

### CPU/GPU RAM split
Memory split between the CPU and GPU can be set in `boot/config.txt`
by adjusting the parameter `gpu_mem` which stands for the amount of RAM in MB
that is available to the GPU (minimum 16, default 64) and the rest is available
to the ARM CPU.

### HDMI/Analogue TV-Out
With the default configuration, the Raspberry Pi uses HDMI video if a HDMI
monitor is connected. Otherwise, it uses analog TV-Out (also known as composite
output or RCA) To turn the HDMI or analog TV-Out on or off, have a look at
`/opt/vc/bin/tvservice`

Use the `-s` parameter to check the status; the `-o`
parameter to turn the display off and `-p` parameter to power on HDMI with
preferred settings.  

Adjustments are likely required to correct proper
overscan/underscan and are easily achieved in `/boot/config.txt` in which many
tweaks are set. To fix, simply uncomment the corresponding lines and setup per
the commented instructions:

```
# uncomment the following to adjust overscan. Use positive numbers if console
# goes off screen, and negative if there is too much border
#overscan_left=16
overscan_right=8
overscan_top=-16
overscan_bottom=-16
```

Or simply disable overscan if the TV/monitor has a "fit to screen" option.

    disable_overscan=1

Users wishing to use the analog video out should consult [this config file](https://raw.github.com/Evilpaul/RPi-config/master/config.txt)
which contains options for non-NTSC outputs.

## Camera
The commands for the camera module are included as part of the raspberrypi-firmware package:
```
$ /opt/vc/bin/raspistill
$ /opt/vc/bin/raspivid
```

Append to `/boot/config.txt`:
```
gpu_mem=128
start_file=start_x.elf
fixup_file=fixup_x.dat
```
Optionally

    disable_camera_led=1

The following is a common error:

```
mmal: mmal_vc_component_enable: failed to enable component: ENOSPC
mmal: camera component couldn't be enabled
mmal: main: Failed to create camera component
mmal: Failed to run camera app. Please check for firmware updates
```

which can be corrected by setting these values in `/boot/config.txt`:
```
cma_lwm=
cma_hwm=
cma_offline_start=
```

Another common error:
```
mmal: mmal_vc_component_create: failed to create component 'vc.ril.camera' (1:ENOMEM)
mmal: mmal_component_create_core: could not create component 'vc.ril.camera' (1)
mmal: Failed to create camera component
mmal: main: Failed to create camera component
mmal: Only 64M of gpu_mem is configured. Try running "sudo raspi-config" and ensure that "memory_split" has a value of 128 or greater
```

can be corrected by adding the following line to `/etc/modprobe.d/blacklist.conf`:

    blacklist i2c_bcm2708

In order to use standard applications (those that look for `/dev/video0`) the
V4L2 driver must be loaded. This can be done automatically at boot by creating
an autoload file, `/etc/modules-load.d/rpi-camera.conf`:

    bcm2835-v4l2

The V4L2 driver by default only allows video recording up to 1280x720, else it
glues together consecutive still screens resulting in videos of 4 fps or lower.
Adding the following options removes this limitation, `/etc/modprobe.d/rpi-camera.conf`:

    options bcm2835-v4l2 max_video_width=3240 max_video_height=2464

## Onboard Hardware Sensors
### Temperature
Temperatures sensors can be queried with utils in the `raspberrypi-firmware` package.

```
$ /opt/vc/bin/vcgencmd measure_temp
temp=49.8'C
```

### Voltage
Four different voltages can be monitored via `/opt/vc/bin/vcgencmd`:

    $ /opt/vc/bin/vcgencmd measure_volts <id>

Where `<id>` is:

* core for core voltage
* sdram_c for sdram Core voltage
* sdram_i for sdram I/O voltage
* sdram_p for sdram PHY voltage

### Watchdog
BCM2708 has a hardware watchdog which can be utilized by enabling the
`bcm2708_wdog` kernel module.  

For proper operation the watchdog daemon also has to be installed, configured
(by uncommenting the "watchdog-device" line in `/etc/watchdog.conf`) and
enabled.  

This should also apply for Raspberry Pi 2 by using the `bcm2709_wdog` module
and Raspberry Pi 3 by using the `bcm2835_wdt` module.

## Hardware Random Number Generator
Arch Linux ARM for the Raspberry Pi had the `bcm2708-rng` module set to load at
boot; starting with kernel 4.4.7 the `bcm2835_rng` module replaces the
former on Raspberry Pi 2 and Raspberry Pi 3 units.  

Install `rng-tools` and
tell the Hardware RNG Entropy Gatherer Daemon (`rngd`) where to find the hardware
random number generator.  This can be done by editing `/etc/conf.d/rngd`:

    RNGD_OPTS="-o /dev/random -r /dev/hwrng" 

and enabling and starting the `rngd` service.  

If `haveged` is running, it should be stopped and disabled, as it might
compete with `rngd` and is only preferred when there is no hardware random number
generator available.  

Once completed, this change ensures that data from the
hardware random number generator is fed into the kernel's entropy pool at
`/dev/random`. To check the available entropy, run: 

    # cat /proc/sys/kernel/random/entropy_avail 
    
The number it reports should be around 3000, whereas before setting up rngd it
would have been closer to 1000.

## I/O Pins
### GPIO
To be able to use the GPIO pins from Python, use the RPi.GPIO library. 
Install the [python-raspberry-gpio](https://aur.archlinux.org/packages/python-raspberry-gpio/) package
from the AUR.

### SPI
To enable the `/dev/spidev*` devices, uncomment the following line in
`/boot/config.txt`:

    device_tree_param=spi=on

### I2C
Install `i2c-tools` and `lm_sensors` packages.

Configure the bootloader to enable the i2c hardware by appending to `/boot/config.txt`:

    dtparam=i2c_arm=on
    
Configure the `i2c-dev` and `i2c-bcm2708` (if not blacklisted for the camera) modules to be loaded at boot in
`/etc/modules-load.d/raspberrypi.conf`:

   i2c-dev
   i2c-bcm2708

Reboot the Raspberry Pi and issue the following command to get the hardware address:

    i2cdetect -y 0
  
Note: When using the I2C1 port instead of I2C0, one will need to run `i2cdetect
-y 1` instead and replace `i2c-0` with `i2c-1` in the following steps.

Now instantiate the device. Change the hardware address to the address found in
the previous step with '0x' as prefix (e.g. 0x48) and choose a device name:

    echo <devicename> <hardware address> >/sys/class/i2c-adapter/i2c-0/new_device
   
Check `dmesg` for a new entry:
    
    i2c-0: new_device: Instantiated device ds1621 at 0x48
    
Finally, read the sensor output:
    sensors
    
### 1-WIRE
To enable the 1-wire interface add this line to `/boot/config.txt` and reboot.
`dtoverlay=w1-gpio`

### Non-Root GPIO

To use the GPIO/SPI pins as a regular non-root user (in group `tty`), add the following lines to a new file `/usr/lib/udev/rules.d/99-spi-permissions.rules`

    KERNEL=="spidev*", GROUP="tty", MODE="0660"
    SUBSYSTEM=="gpio*", PROGRAM="/bin/sh -c 'chown -R root:tty /sys/class/gpio && chmod -R 775 /sys/class/gpio; chown -R root:tty /sys/devices/virtual/gpio && chmod -R 775 /sys/devices/virtual/gpio; chown -R root:tty /sys/devices/platform/soc/*.gpio/gpio && chmod -R 775 /sys/devices/platform/soc/*.gpio/gpio'"
    SUBSYSTEM=="gpio", KERNEL=="gpiochip*", ACTION=="add", PROGRAM="/bin/sh -c 'chown root:tty /sys/class/gpio/export /sys/class/gpio/unexport ; chmod 220 /sys/class/gpio/export /sys/class/gpio/unexport'"
    SUBSYSTEM=="gpio", KERNEL=="gpio*", ACTION=="add", PROGRAM="/bin/sh -c 'chown root:tty /sys%p/active_low /sys%p/direction /sys%p/edge /sys%p/value ; chmod 660 /sys%p/active_low /sys%p/direction /sys%p/edge /sys%p/value'"


## See Also

* [Raspberry Pi](https://www.raspberrypi.org/) - Official website
* [RPi Config](http://elinux.org/RPiconfig) - Excellent source of info relating to under-the-hood tweaks.
* [RPi vcgencmd usage](http://elinux.org/RPI_vcgencmd_usage) - Overview of firmware command vcgencmd.
* [Arch Linux ARM on Raspberry Pi](http://archpi.dabase.com/) - A FAQ style site with hints and tips for running Arch Linux on the RPi

