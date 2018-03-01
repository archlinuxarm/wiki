## USB network interface
In order use the USB network interface you need to load the USB gadget kernel module:

```bash
sudo modprobe g_ether
```

Or, if you want to load this module every time you boot:

```bash
echo g_ether > /etc/modules-load.d/g_ether.conf
```

Then, the interface should be visible as `usb0`.

## IO
There is an extensive BeagleBone IO [python library](https://github.com/adafruit/adafruit-beaglebone-io-python) from Adafruit.
