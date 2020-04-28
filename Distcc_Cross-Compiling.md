## Introduction
*Disclaimer: This guide will appear vague and incomplete if you aren't sure what you're doing.  This is intentional.  This is specifically not designed for users new to software compilation and toolchain components.*

This is the official cross-compiling method used at Arch Linux ARM. If you plan on building a lot of packages and want to speed up the process, the following guide will turn an x86 Linux computer into an ARM cross-compiler. It's also much easier than most cross-compile setups.

This guide makes use of distcc in order to not have to build a full ARM development environment on x86. As the distcc project website states, "distcc does not require all machines to share a filesystem, have synchronized clocks, or to have the same libraries or header files installed." This is particularly advantageous to us since all that is needed is a working cross-compiler for ARM on a faster machine, while controlling the build from an ARM computer that has all of the current libraries and headers.

## Distcc
Before beginning, a distcc environment needs to be set up. Follow the [[Distributed Compiling]] guide to establish a master system. The x86 machines will be known as the clients.

## Pre-built crosstool-ng toolchains
In lieu of building the toolchain as detailed below, if you are running a 64-bit Linux installation you can use these packaged toolchains that are employed in the official build system.

It is *highly recommended* to use these tarballs as they have been thoroughly tested, and are maintained to be version- and source-matched to the current toolchain components for our ARM architectures.  If you build the toolchain yourself, you must assemble patched source tarballs to build versions that match what is in use here.

* [ARMv5te soft-float](/builder/xtools/x-tools.tar.xz) (2e17110e55bd82abbbcb58afc5f0a165)
* [ARMv6l hard-float](/builder/xtools/x-tools6h.tar.xz) (834338535921e0208f03bd23b49097b6)
* [ARMv7l hard-float](/builder/xtools/x-tools7h.tar.xz) (67002d06aab2258f5043497087b33555)
* [ARMv8](/builder/xtools/x-tools8.tar.xz) (a197dedc3134a2e7c7221494ba4ebeff)

## Building a crosstool-ng toolchain
This process is very automated, courtesy of [crosstool-ng](http://crosstool-ng.org). As a normal user (<b>not root!</b>), clone revision ec384f2b of the git repository into a directory called "cross" in your home directory. Enter the source directory and configure with a prefix for the "cross" directory, make, and make install.  If you are missing any pre-requisites, the configure script will let you know what they are.

```
mkdir -p cross/src
cd cross
git clone https://github.com/crosstool-ng/crosstool-ng.git
cd crosstool-ng
git checkout ec384f2b
./bootstrap
./configure --prefix=/home/your_user/cross
make
make install
```

At this point crosstool-ng is ready to be configured. The program "ct-ng" in the "bin" directory is where the magic happens. It also has a menu configuration like the Linux kernel.

## Downloading a CrossTool Configuration
Download the default .config file to place in "~/cross/bin" as shown below. <b>Once you do this, do not run "menuconfig" or values will be overwritten</b>.  Choose either v5, v6, v7, or v8 depending on the target platform.

```
cd /home/your_user/cross/bin
wget http://archlinuxarm.org/builder/xtools/xtools-dotconfig-[v5|v6|v7|v8] -O .config
```

## Build the cross-toolchain

```
cd /home/your_user/cross/bin
./ct-ng build
```

## Make nice with distcc
The toolchain will install under "~/x-tools" for armv5, "~/x-tools6h" for armv6 hard-float, "~/x-tools7h" for armv7 hard-float, and "~/x-tools8" for AArch64.  You can move this somewhere else if you like, or leave it where it is. Before we can use the compiler binaries that were created, links need to be created to make their names more appropriate. When compile jobs are sent to distcc, the program specified in the CC environment variable on the build master is what gets executed on the build clients. All the binaries that have been produced by crosstool-ng are in the correct format specifying the target platform as a prefix, though not with the correct platform and lacking tuple-less variants. This script will fix our problem. Make "~/x-tools[6h|7h|8]/arm-unknown-linux-gnueabi[hf]/bin/" writable and in there create a file called "link" and paste the following into it. Uncomment for your target architecture and run it.

```bash
#!/bin/bash
for file in `ls`; do
        if [[ "$file" == "link" ]]; then
                continue
        fi
        # uncomment for v5
        # ln -s $file ${file#arm-unknown-linux-gnueabi-}
        # ln -s $file armv5tel-unknown-linux-gnueabi-${file#arm-unknown-linux-gnueabi-}
        # uncomment for v6
        # ln -s $file ${file#arm-unknown-linux-gnueabihf-}
        # ln -s $file armv6l-unknown-linux-gnueabihf-${file#arm-unknown-linux-gnueabihf-}
        # uncomment for v7
        # ln -s $file ${file#arm-unknown-linux-gnueabihf-}
        # ln -s $file armv7l-unknown-linux-gnueabihf-${file#arm-unknown-linux-gnueabihf-}
        # uncomment for v8
        # ln -s $file ${file#aarch64-unknown-linux-gnu-}
done
```

Now the "bin" directory contains links with names that distcc will play nice with.

## Compile!
Back on ARM master device, make sure that distcc has been enabled in makepkg.conf per the [[Distributed Compiling]] guide.  Now all builds using makepkg will make use of the distcc and cross compiler setup.

## Crap, it's not working
If you've followed this guide to the letter and you know you've done everything right, the likely problem is that the user distccd is running as does not have permission to access the location of the crosstool-ng binaries.  Either change distccd's user or relocate the x-tools directory to somewhere it can read it, then be sure the PATH set above reflects the new location.
