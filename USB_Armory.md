## LED Brightness
If the default settings are too bright, disable the default modules using
`/etc/modprobe.d/led.conf`:

```
blacklist leds_gpio
blacklist led_class
blacklist ledtrig_heartbeat
```

and use a systemd tmpfile to configure desirable settings,
`/etc/tmpfiles.d/led.conf`:

```
w /sys/class/gpio/export - - - - 123
w /sys/class/gpio/gpio123/direction - - - - in
```
