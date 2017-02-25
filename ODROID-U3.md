## HDMI audio

To enable audio over HDMI, add the following to your asound.conf:

```
pcm.!default {
   type hw
   card 0
}
ctl.!default {
   type hw
   card 0
}
```
## LEDs
ODROIDs have dual LEDs: a red power LED which is always on if power is
supplied, and a blue LED which can be configured.

By default, the blue LED is a heartbeat LED, which flashes when the kernel is
running. This can be configured in `/sys/class/leds/led1/trigger`.

To list available triggers:

    # cat /sys/class/leds/blue:heartbeat/trigger

Replace TRIGGER with one of the available triggers. This setting will apply
instantly, but be lost upon reboot.

    # echo TRIGGER > /sys/class/leds/blue:heartbeat/trigger

To create a persistent configuration, edit or create
`/etc/tmpfiles.d/leds.conf`:

    w /sys/class/leds/blue:heartbeat/trigger - - - - TRIGGER


