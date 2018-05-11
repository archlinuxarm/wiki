If you have spare ARM or x86 systems, using distcc is a very simple way to get all of them in on compiling packages.  makepkg is already set up for using distcc, all that's needed is to enable the functionality.

For distributing to other ARM systems, you will need to have base-devel installed.  Each system needs to have the same packages installed that are required for compiling the package you're about to build using this method. See the [Arch Wiki](https://wiki.archlinux.org) for makepkg, pacman, etc.

For distributing to x86 systems, install the cross compilers for the needed targets from the [[Distcc Cross-Compiling]] page.

Install distcc on all systems to be used:

```
pacman -S distcc
```

## Configuration: Introduction
For the purposes of this example, all of the hosts are in the local network of 10.3.0.0/24, and access is allowed from that entire subnet.  If you want to restrict access further, you will have to specify individual IPs.  Be sure to change the address from the examples to fit your local network configuration.

Additionally, this example assumes that you are starting compiles from one specific system (master), which is sending compiles out to the other system(s) (client).  If you want to compile from any system, simply apply the client configuration to the master system, and the master system configuration to the client system(s).

If you want to use the master system to compile as well, apply the client configuration to it.

## Configuration: Master System
These are the configurations for the ARM system you'll be running *makepkg* from.

In /etc/makepkg.conf, or in makepkg.conf in your chroot, change BUILDENV to un-negate the distcc option:

```
BUILDENV=(distcc color !ccache check !sign)
```

Change DISTCC_HOSTS to a space-delimited list of the distcc clients on your network.  If you want to use the master system, be sure to put it's IP address in there too.  The hosts listed will be prioritized in the order listed.  To keep configurations simple, use the LAN IP address for the local system, not "localhost" or "127.0.0.1".

```
DISTCC_HOSTS="10.3.0.6 10.3.0.2"
```

Additional options can be specified in DISTCC_HOSTS to control how distcc uses the hosts.  For example, to randomize which host is used, limit the maximum number of jobs sent to the first host to 4 and the second host to 8, and to specify a different port that distccd is running on:

```
DISTCC_HOSTS="--randomize 10.3.0.6:3932/4 10.3.0.2:3932/8"
```

Change the MAKEFLAGS -j flag to reflect the total number of processors available on the master system.  The common wisdom is to set this to the number of physical cores + 1.  Keep in mind that only compiles are distributed; preprocessing and linking still takes place on the master system.  Therefore, this number should reflect the capabilities of the master system and not the total number of distributed cores available.

```
MAKEFLAGS="-j3"
```

## Configuration: Client System(s)
These are the configurations for a second ARM system or x86 cross-compiling computer.

In /etc/conf.d/distccd, change DISTCC_ARGS to reflect the hosts or network you're allowing to connect and the number of distccd instances (jobs) to start.  These arguments will be passed to distccd when it's started, so this is a good place to add any additional arguments you want.

```
DISTCC_ARGS="--allow 10.3.0.0/24 --jobs 20"
```

As of distcc 3.3, protections for [CVE-2004-2687](https://nvd.nist.gov/vuln/detail/CVE-2004-2687) have been implemented.  Specifically, the symlinks to distcc in /usr/lib/distcc are a whitelist of what executables are allowed to be run through distcc.  For cross-compiling systems, this means that symlinks of each ARM architecture's compiler must be created (c++, cc, cpp, g++, and gcc).  For example:

```
ln -s /usr/bin/distcc /usr/lib/distcc/aarch64-unknown-linux-gnu-gcc
```

Alternately, if you are confident in your security, you can disable the protections by adding `--make-me-a-botnet` to DISTCC_ARGS.

### systemd services
On ARM systems where the installed toolchain will be used, simply enable the shipped distccd service:

```
systemctl enable distccd
systemctl start distccd
```

On x86 systems where a cross toolchain will be used, create a new systemd service file for the toolchain.  When setting up for cross-compiling multiple ARM architectures, each service file should start distcc with different ports from the default (3632) and specify a unique log file.  The new port number must be set in DISTCC_HOSTS in makepkg.conf.  This example shows a service file for the AArch64 toolchain in a multiple toolchain environment:

`/etc/systemd/system/distccd8.service`

```
[Unit]
Description=A distributed C/C++ compiler (ARMv8)
Documentation=man:distccd(1)
After=network.target

[Service]
User=myuser
EnvironmentFile=/etc/conf.d/distccd
Environment="PATH=/home/myuser/x-tools8/aarch64-unknown-linux-gnu/bin:/usr/bin"
ExecStart=/usr/bin/distccd --no-detach --daemon $DISTCC_ARGS --log-file=/var/log/distccd8.log --port 3932 --stats-port 3933

[Install]
WantedBy=multi-user.target
```

This service sets the user that distccd will run as, which must be able to access and execute the toolchain binaries.  It sources the contents of `/etc/conf.d/distcc` into the environment so the DISTCC_ARGS variable is available.  It then sets the path, placing the bin directory of the AArch64 toolchain at the front.  The distccd daemon is started with the options from DISTCC_ARGS, a unique log file, and port numbers for distccd and the stats server (which can be enabled with the `--stats` option).

Depending on the user distccd is run as and the permissions of the log directory, you may need to create the log file prior to starting the service and set its owner to that user.

The service can then be enabled and started:

```
systemctl enable distccd8
systemctl start distccd8
```

You can now run *makepkg* as you normally would and the hosts specified will start receiving work. If the hosts are unavailable, the package will simply compile locally.
