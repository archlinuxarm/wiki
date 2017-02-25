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

## CPU Fan
The optional CPU fan can be controlled via the `/sys/devices/platform/odroidu2-fan` 
interface.

```
# echo auto > /sys/devices/platform/odroidu2-fan/fan_mode
# echo manual > /sys/devices/platform/odroidu2-fan/fan_mode
```

### Fan Speed (Manual Only)
Values range from 0 (0%) to 255 (100%)

```
# echo 0 > /sys/devices/platform/odroidu2-fan/pwm_duty
# echo 255 > /sys/devices/platform/odroidu2-fan/pwm_duty
```

