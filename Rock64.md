## UART
UART is accessible on the Pi-2 bus (same pins as on a Raspberry Pi), using a baud rate of 1500000.

## MAC Address
The MAC address for the system is configurable in /boot/boot.txt.  Use spaces instead of colons between each octet.  After making changes, run the *mkscr* script from within /boot.

## Audio
Audio can be enabled by installing `dtc` and issuing
```
fdtput -t s /boot/dtbs/rockchip/rk3328-rock64.dtb /analog-sound status okay
reboot
```
