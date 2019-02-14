## Additional Packages

* X.org video driver package: xf86-video-odroid-c1
  Note: Add your user to the "video" group to be able to access /dev/mali and /dev/ump.
* Mali EGL and GLES drivers (installed with xf86-video-odroid-c1): odroid-c1-libgl
* wiringPi modified for the ODROID-C1: wiring-odroid
* Kodi: kodi-c1

## IR

To make use of the IR receiver, load module meson-ir

## PWM

For one PWM (pin 33) and one SPI:

```
sudo modprobe pwm-peson npwm=1
sudo modprobe pwm-ctrl
```

For two PWM (pins 33 and 19) and no SPI

```
sudo modprobe pwm-peson npwm=2
sudo modprobe pwm-ctrl
```

You can control the PWM via simple sysfs entries. In /sys/devices/platform/pwm-ctrl you'll find the following files:

* duty0: Duty cycle in percent: `echo 50 > duty0 # 50% duty cycle`
* enable0: Enable/Disable this PWM: `echo 1 > enable0 # enable PWM0`
* freq0: PWM Frequency in Hertz: `echo 100000 > freq0 # set frequency to 100kHz`
    * On PWM0 you can test higher frequencies; however, 1Mhz is the maximum supported frequency.
* Note: If you are using the two PWM setup, you will have: enable0, freq0, duty0 AND enable1, freq1, duty1.
