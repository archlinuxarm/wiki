## Xorg

* The PowerVR GX6250 GPU is not supported at this time.  Therefore, no particular Xorg drivers are needed.  The built in modesetting driver will correctly detect the display.
* xf86-input-libinput is preferred over evdev and synaptics for the best experience with the touchpad and touchscreen.

## Audio
Many, many channels must be unmuted and their volume turned up, so this handy script will make sure all are unmuted.
```
COUNT=0
while [ $COUNT -lt 200 ]; do echo $COUNT; amixer -c 0 cset numid=$COUNT on; let COUNT=COUNT+1; done
```

Some of the channels must also have their volume increased.

The following channels must be unmuted for the speakers to work.  The alsamixer utility (part of alsa-utils) can be used to do this by navigating with the arrow keys and pressing **m** to unmute the channel.

* Speaker Channel
* DAC MIXL DAC L2
* DAC MIXL DAC R2
* DAC MIXL Sto DAC Mix L
* DAC MIXR DAC L2
* DAC MIXR DAC R2
* DAC MIXR Sto DAC Mix R
* DAC1 MIXL DAC1
* DAC1 MIXL Stereo ADC
* DAC1 MIXR DAC1
* DAC1 MIXR Stereo ADC
* 003 I05
* 004 I06
* SPK MIXL DAC L1
* SPK MIXR DAC R1
* SPKVOL L
* SPKVOL R
* SPOL MIX DAC L1
* SPOL MIX DAC R1
* SPOL MIX SPKVOL L
* SPOL MIX SPKVOL R
* SPOR MIX DAC R1
* SPOR MIX SPKVOL R

You may also want to decrease the DAC1 gain value, otherwise audio will still be fairly loud at the lowest setting.

## Additional ARM steps

### Move from USB stick to SDCard
As noted in the [[Installation]], targeting the`SDCard from ChromeOS for boot & root partitions has caused I/O issues but the USB install can be moved to SDCard _after_ ArchLinux successfully boots from the USB stick.  Moving to a SDCard can free up a USB port.

* NOTE: I'd suggest SDHC, not SDXC because of exfat partitions, and UHS-I (SDHC I) ultra high speed bus cards, instead of just the older Class 1-10 speed designations. (UHS-II,-III are not yet supported by the kernel.)
* CAUTION: the Chromebook eMMC "disk" is often /dev/mmcblk0 (because all flash drives are types of MMC) so avoid overwriting it!  The correct device should be whatever your SDCard is detected, as seen in logs (journalctl) when inserting the SDCard into the Chromebook slot.
```
pacman -S cgpt e2fsprogs
badblocks -wsv -t random /dev/mmcblk1
```
* INFO: badblocks was one utility that I found whose writing option had the side effect of getting past errors from SDCards that had been formatted by Windows, and that neither fdisk nor cgpt could not initially overwrite.

Follow the [[Installation]] up until copying files.
```
mkdir /mnt/new-root
mount /dev/mmcblk1p2 /mnt/new-root
rsync -axX / /mnt/new-root
```
Grab a cup of coffee/tea until the sync finishes.
```
umount /mnt/new-root
reboot
```

### Improve performance
ARM processors do have similar performance tuning abilities as x86 based processors but typical tooling to do the tweaking isn't the same.  Example: cpupower doesn't work for ARM.  So alternative setups are needed, like getting systemd to call the old rc.local on each boot to set a CPU governor.
```
sudo -i
echo 'echo performance > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor' >> /etc/rc.local
sudo pacman -S rc-local
sudo systemctl enable rc-local.service
sudo systemctl start rc-local.service
```

## Optional steps for daily use Chromebook

### Enable automatic network control
Because we may need to reboot, wifi-menu can get tedious, or your Chromebook should almost always be connected to Wifi, so enable the network stack to be automatically controlled and enabled.
```
pacman -S netctl
wifi-menu -o
ls /etc/netctl/
netctl enable mlan0-beercade
netctl start mlan0-beercade
ifconfig 
ping 8.8.4.4
```

### Install Desktop Environment
A good, lightweight desktop environment, like Xfce, along with a window manager (xfwm), started by a lightweight desktop manager, like LXDM, on graphical boot; can all be very usable on the Lenovo Chromebook.
```
pacman -S xfce4 xfce4-goodies xfce4-session xorg-xinit xorg xfwm4 lxdm
systemctl enable lxdm.service
reboot
```

### Add yourself a User, with privileges
As best practice, create a user for yourself who can gain local administrator privileges when needed.
```
useradd -m justin
passwd justin
usermod -aG wheel,systemd-journal,sys,log,games,audio,video justin
pacman -S sudo
visudo
```
 uncomment one of the wheel lines
```
su - justin
groups
```

You'll want to re-login to any sessions, like Xfce, as your new user to make sure all group memberships are working as expected.

### Sync time & set Timezone
Set your system time to be offset by your local timezone.  In many cases (like in dual-boot scenarios), it's better to keep the hardware clock set to UTC and let system time be set offset by timezone and synchronized by a network synchronization service.
```
sudo hwclock --show
sudo pacman -S timedatectl
timedatectl
sudo timedatectl set-timezone America/Chicago
```
set this ^ parameter to your local timezone
```
sudo systemctl status systemd-timesyncd
```

