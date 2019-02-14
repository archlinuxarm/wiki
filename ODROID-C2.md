## Additional Packages

* X.org video driver package: xf86-video-odroid-c2
    * Note: Add your user to the "video" group to be able to access /dev/mali and /dev/ump.
* X11 Mali EGL and GLES drivers (installed with xf86-video-odroid-c2): odroid-c2-libgl-x11
    * Note: Reboot after installation for udev rules to take effect.
* Framebuffer Mali EGL and GLES drivers: odroid-c2-libgl-fb
* wiringPi modified for the ODROID-C2: wiring-odroid

## Headless Mode
For servers (ie, no need for a display), uncomment the following in
`/boot/boot.ini` to free up approx 300M of memory at the cost of disabling the
video subsystem:

    setenv nographics "1"

## LEDs
ODROIDs have dual LEDs: a red power LED which is always on if power is
supplied, and a blue LED which can be configured.

By default, the blue LED is a heartbeat LED, which flashes when the kernel is
running. This can be configured in `/sys/class/leds/blue:heartbeat/trigger`.

To list available triggers:

    # cat /sys/class/leds/blue:heartbeat/trigger

Replace TRIGGER with one of the available triggers. This setting will apply
instantly, but be lost upon reboot.

    # echo TRIGGER > /sys/class/leds/blue:heartbeat/trigger

To create a persistent configuration, edit or create
`/etc/tmpfiles.d/leds.conf`:

    w /sys/class/leds/blue:heartbeat/trigger - - - - TRIGGER

