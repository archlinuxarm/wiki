## Additional Packages

* X.org video driver package: xf86-video-fbdev
* Mali EGL and GLES drivers (currently framebuffer only): odroid-c2-libgl-fb
* wiringPi modified for the ODROID-C1/C2: wiringc1

## Display

Configuring the resolution of the screen on the HDMI port is done through ``/boot/boot.ini``, and those values are loading during boot by ``amlogic.service`` from ``/proc/cmdline``. At this time, the changing resolution settings via ``boot.ini`` requires a reboot for changes to take effect.

When setting your desired resolution in ``boot.ini``, ensure that only one resolution line is uncommented as further entries will override any previous.
