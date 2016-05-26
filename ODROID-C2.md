## Additional Packages

* X.org video driver package: xf86-video-odroid-c2
    * Note: Add your user to the "video" group to be able to access /dev/mali and /dev/ump.
* X11 Mali EGL and GLES drivers (installed with xf86-video-odroid-c2): odroid-c2-libgl-x11
    * Note: Reboot after installation for udev rules to take effect.
* Framebuffer Mali EGL and GLES drivers: odroid-c2-libgl-fb
* wiringPi modified for the ODROID-C2: wiringc1
