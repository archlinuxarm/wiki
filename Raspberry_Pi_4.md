## Kernel

Follow the install instructions at the bottom of
<https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-4#installation>
 and boot.

Install linux-rpi as kernel and verify /boot/cmdline.txt to point to the right
root device before rebooting.

## Boot from USB drive

The RPI4 supports booting from USB out of the box.

Refer to <https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#usb-boot-modes>

Most documentation assumes booting from mmcblk devices. Make sure you always
point to the right devices or partitions, particularly in /boot/cmdline.txt
and /etc/fstab.

## Prefer AArch64 for high IO loads and out-of-memory issues

The RPI4 has issues with a high IO load when running in 32-bit mode. Running
AArch64 solves these. If you experience the oom-killer kicking in with no
obvious memory hungry apps but high IO, then switch to AArch64.

Sources:

- <https://github.com/raspberrypi/linux/issues/3210#issuecomment-716510916>
  contains the hint to not use 32-bit.
- <https://forums.raspberrypi.com/viewtopic.php?t=249924>
- <https://forums.raspberrypi.com/viewtopic.php?t=67204>
