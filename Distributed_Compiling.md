If you have multiple devices, using distcc is a very simple way to get all of them in on compiling packages.  makepkg is already set up for using distcc, all that's needed is to enable the functionality.

Before you begin, you will need to make sure you have base-devel installed.  Each device will need to have the same packages installed that are required for compiling the package you're about to build using this method. See the [Arch Wiki](https://wiki.archlinux.org) for makepkg, pacman, etc.

Install distcc on all devices to be used:

```
pacman -S distcc
```

## Configuration: Introduction
For the purposes of this example, all of the hosts are in the local network of 10.3.0.0/24, and access is allowed from that entire subnet.  If you want to restrict access further, you will have to specify individual IPs.  Be sure to change the address from the examples to fit your local network configuration.

Additionally, this example assumes that you are starting compiles from one specific device (master), which is sending out to the other device(s) (client).  If you want to compile from any device, simply apply the client configuration to the master device, and the master device configuration to the client device(s).

If you want to use the master device to compile as well, apply the client configuration to it. It is only suggest to use the master if all slaves are the same architecture as the master (ARM).

## Configuration: Master Device
This is what you run on the ARM device you'll be running "makepkg" from and using to coordinate builds.

In /etc/makepkg.conf, change BUILDENV to un-negate the distcc option:

```
BUILDENV=(distcc color !ccache check !sign)
```

Change DISTCC_HOSTS to a space-delimited list of the distcc clients on your network.  If you want to use the master plug, be sure to put it's IP address in there too.  The hosts listed will be prioritized in the order listed.  To keep configurations simple, use the LAN IP address for the local plug, not "localhost" or "127.0.0.1".

```
DISTCC_HOSTS="10.3.0.6 10.3.0.2"
```

Change MAKEFLAGS -j flag to reflect the total number of processors available +1.  For example, with 2 plugs (1 master and 1 client):

```
MAKEFLAGS="-j3"
```

## Configuration: Client Device(s)
This is what you'd run on a second ARM device or a cross-compiling computer.

In /etc/conf.d/distccd, change DISTCC_ARGS to reflect the hosts or network you're allowing to connect:

```
DISTCC_ARGS="--user nobody --allow 10.3.0.0/24"
```

Enable and start the distcc daemon:

```
systemctl enable distccd
systemctl start distccd
```

You can now run *makepkg* as you normally would and the hosts specified will start receiving work. If the hosts are unavailable, the package will simply compile locally.
