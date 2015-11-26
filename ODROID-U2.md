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