
## Prepare your host environment ##
### Hardware ###
To work with Beagleboard or OMAP3EVM you will need the following:
  * Board
  * Host machine (Linux Ubuntu 8.x/9.04) with Ethernet card and serial port
  * Serial cable (null-modem)
  * Power supply (5V)
  * USB Hub
  * USB Ethernet adapter (for Beagleboard)
  * USB Keyboard & Mouse
  * Ethernet cross cable or Ethernet Switch/Hub+2 cables
  * Card Reader
  * 2Gb SD or MMC Card
  * 4:3 or 16:9 capable Display with DVI input
  * HDMI to DVI cable (for BeagleBoard or IGEPv2)
  * DVI to DVI (if you want to use external Display on OMAP3EVM)

### Software ###
As Google we recommend to use Ubuntu 8.04+ , but also you can use CentOS 5.3 (32 bit).
  * Install DHCP server
  * Install tftp server (actual for OMAP3EVM)
  * Install [repo](http://source.android.com/download/using-repo) tool
  * Set up your Linux development environment, make sure you have the following:
Required packages:

```
Git 1.5.4 or newer and the GNU Privacy Guard. 
JDK 5.0, update 12 or higher.  Java 6 is not supported, because of incompatibilities with @Override.
flex, bison, gperf, libsdl-dev, libesd0-dev, libwxgtk2.6-dev (optional), build-essential, zip, curl, minicom, tftp-server, uboot-mkimage
```
For Ubuntu 32-bit use such command:
```
$ sudo apt-get install git-core gnupg sun-java5-jdk flex bison gperf libsdl-dev libesd0-dev libwxgtk2.6-dev build-essential zip curl libncurses5-dev zlib1g-dev minicom tftpd uboot-mkimage
```
Ubuntu Intrepid (8.10) users may need a newer version of libreadline:
```
$ sudo apt-get install lib32readline5-dev
```
For DSP users:
```
$ sudo apt-get install expect
```

## Configure your network ##
  * Configure your host Ethernet adapter
```
sudo ifconfig ethX 10.10.10.1 netmask 255.255.255.0 up
```
  * Configure DHCP server for target's network
```
; Example dhcpd.conf
default-lease-time 600;
max-lease-time 7200;
subnet 10.10.10.0 netmask 255.255.255.0 {
  range 10.10.10.2 10.10.10.10;

host beagleboard_rev_c3 {
  hardware ethernet 00:80:C8:xx:xx:xx;
  fixed-address 10.10.10.2;
}

host omap3evm_rev_d {
  hardware ethernet 00:50:c2:xx:xx:xx;
  fixed-address 10.10.10.3;
  filename "evm/uImage"; <- this string actual for boot with tftp 
}

```
  * Configure TFTP-server
/etc/xinet.d/tftpd:
```
service tftp
{
protocol        = udp
port            = 69
socket_type     = dgram
wait            = yes
user            = nobody
server          = /usr/sbin/in.tftpd
server_args     = /tftpboot
disable         = no
}
```
## Checkout sources ##
```
$ mkdir rowboat-android
$ cd rowboat-android
$ repo init -u git://gitorious.org/rowboat/manifest.git -m ManifestName
$ repo sync
```
Where _ManifestName_ is:
  * rowboat-donut.xml - for donut version of rowboat
  * rowboat-donut-dsp.xml - for donut version of rowboat with DSP support
  * rowboat-eclair.xml - for eclair version of rowboat
  * rowboat-eclair-dsp.xml - for eclair version of rowboat with DSP support
  * rowboat-froyo.xml - for froyo version of rowboat

## Build rowboat ##

For froyo and eclair version of rowboat you may build kernel, filesystem, dvsdk (in case of dsp version) and install sgx drivers using the command below. After this step you may skip "Build kernel", "Build rootfs", "Install the Android Graphics SGX SDK on Host Machine", "Install the drivers in Target filesystem" sections and go to "Populate the root filesystem".

```
$ make TARGET_PRODUCT=(beagleboard|omap3evm|igepv2|am3517evm) [TARGET_BUILD_VARIANT=tests] [-j8] [OMAPES=(2.x|3.x|5.x)]
```
Your must specify your board with TARGET\_PRODUCT variable. To build tests TARGET\_BUILD\_VARIANT may be used (see "Build rootfs" section for more info). It is recommended to use more than one thread on multi core systems, specify -j _number\_of\_threads_ for this. Set OMAPES variable to install proper version of SGX driver (Default is 3.x):
```
For OMAP3530 ES 1or2 = 2.x
For OMAP3530 ES3.0   = 3.x
For AM3517           = 3.x
For AM37x           = 5.x
```
Once make is finished, you can find kernel uImage in kernel/arch/arm/boot/ directory.

## Build kernel ##

While building rowboat-eclair-dsp, this (Build kernel) and next (Build rootfs) steps are not needed. Please refer to the instructions in wiki [DSP stack integration](http://code.google.com/p/rowboat/wiki/DSP#Building_and_Testing_DSP_stack) to build Eclair with DSP integrated.

The next step is building Linux kernel for your board. (If dvsdk target is specified while building rootfs, kernel is already built.) In following listing the first step is settings up prebuilt toolchains, the third step is kernel configuring and here you need to use right config file for your board
  * omap3\_evm\_android\_defconfig is for OMAP3EVM
  * omap3\_beagle\_android\_defconfig is for Beagleboard
  * igep0020\_android\_defconfig is for IGEPv2
  * am3517\_evm\_android\_defconfig is for AM3517 EVM

The fourth step in listing is kernel build command, here we tell `make` utility what build is for `ARM` architecture using cross-toolchains for ARM, the build target is `uImage` and it’s multithread build (-j8):
```
$ cd <sources top>
$ export PATH=${PWD}/prebuilt/linux-x86/toolchain/arm-eabi-4.4.0/bin:$PATH
$ cd kernel/
$ make ARCH=arm omap3_evm_android_defconfig
$ make ARCH=arm CROSS_COMPILE=arm-eabi- uImage -j8
$ cd ..
```

## Build rootfs ##
The next step is Android file system build.
To build file system you need to specify for which board you want to build. Use variable `TARGET_PRODUCT` for this:
  * beagleboard
  * omap3evm
  * igepv2
  * am3517evm

Also if you need some unit tests for further use `TARGET_BUILD_VARIANT=tests`.
If you are using multicore CPU in your host it’s possible to use more than one core for building Android for this pass -j8 (or 16, it depends on cores/CPUs) parameter to `make` command.

In following listing we build Android for OMAP3EVM board with unit test packages and use 8 threads to speed up build process:
```
$ make TARGET_PRODUCT=omap3evm TARGET_BUILD_VARIANT=tests -j8 droid
```

After build process finished you'll need rootfs for this do following:

```
$ cd out/target/product/omap3evm
$ mkdir android_rootfs
$ cp -r root/* android_rootfs
$ cp -r system android_rootfs
```

```
NOTE: IF YOU WANT TO INSTALL THE OPENGL SGX (3D Hardware accelerator) DRIVERS INTO
FILESYSTEM THEN SKIP THE BELOW STEP AND CONTINUE WITH THE Android Graphics Installation 
procedure below.
```

```
$ sudo ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2
```

## Install the Android Graphics SGX SDK on Host Machine ##

The procedure to install Android graphics SDK sources are mentioned below, you should have downloaded the sources as directed in [Get source code wiki](http://code.google.com/p/rowboat/wiki/Source)

### Execute the installer ###
```
#> ./ti_android_sgx_sdk/OMAP35x_Android_Graphics_SDK_setuplinux_3_01_00_03.bin
```
Will result in following instruction, press "Y"
```
This will install TI OMAP35x/AM37x Android Graphics SDK on your computer.
Continue? [n/Y] Y
```

### Accept the License Agreement ###

Kindly read the complete License agreement by pressing Space bar and agree when prompted by pressing "Y"

```
OMAP3530 / OMAP3515 / AM3517 / AM3715 / DM3730
Android Linux Graphics SDK Software License Agreement


IMPORTANT - PLEASE READ THE FOLLOWING LICENSE AGREEMENT CAREFULLY.  THIS IS A
LEGALLY BINDING AGREEMENT.  AFTER YOU READ THIS LICENSE AGREEMENT, YOU WILL BE
ASKED WHETHER YOU ACCEPT AND AGREE TO THE TERMS OF THIS LICENSE AGREEMENT.  DO
NOT CLICK "I HAVE READ AND AGREE" UNLESS: (1) YOU ARE AUTHORIZED TO ACCEPT AND
AGREE TO THE TERMS OF THIS LICENSE AGREEMENT ON BEHALF OF YOURSELF AND YOUR
COMPANY; AND (2) YOU INTEND TO ENTER INTO AND TO BE BOUND BY THE TERMS OF THIS
LEGALLY BINDING AGREEMENT ON BEHALF OF YOURSELF AND YOUR COMPANY.

Important - Read carefully:  This OMAP3530 / OMAP3515 / AM3517 / AM3715 / DM3730
Graphics Software License Agreement ("Agreement") is a legal agreement between
you (either an individual or entity) and Texas Instruments Incorporated ("TI").
The "Licensed Materials" subject to this Agreement include the software programs
(in whole or in part) set forth in Exhibit 1, which is attached hereto and
incorporated herein by this reference, that accompany this Agreement and that TI
has granted you access to download and any "on-line" or electronic documentation
(in whole or in part) associated with these programs, as well as any updates or
upgrades to such software programs and documentation, if any, provided to you at
-- Press space to continue or 'q' to quit --
```
Keep Pressing Space Bar on Keyboard until see
```
                        ========================
                        END OF LICENSE AGREEMENT
                        ========================

Do you agree to the licensing terms [n/Y] Y
```

### Select the source install location ###

```
Where do you want to install TI OMAP35x/AM37x Android Graphics SDK?

[/opt/OMAP35x_Android_Graphics_SDK_3_01_00_03] /home/user/OMAP35x_Android_Graphics_SDK_3_01_00_03

Installing TI OMAP35x/AM37x Android Graphics SDK...
```
Should see following directory structure
```
root@android-pc:~/OMAP35x_Android_Graphics_SDK_3_01_00_03# ls
ANDROID_RN_3_01_00_03.pdf	GFX_Linux_SDK		
gfx_rel_es5.x_android		Makefile.KM.Android
ANDROID_RN_3_01_00_03.txt	gfx_rel_es2.x_android	initrc.patch
Rules.make			GFX_Linux_KM		gfx_rel_es3.x_android  
Makefile			uninstall
```

### Edit Rules.make to configure the SGX source build ###
```
1) #set home area HOME (relative location for all SDK operations)
HOME=INVALIDVAL

2) #Current Directory where Graphics SDK is installed
GRAPHICS_INSTALL_DIR=$(HOME)/OMAP35x_Android_Graphics_SDK_3_01_00_03

3) #Path of Android Root FS
ANDROID_ROOT=$(HOME)/INVALIDVAL

4) #set toolchain root path for arm-eabi
CSTOOL_DIR=INVALIDVAL

5) #set the kernel installation path
KERNEL_INSTALL_DIR=$(HOME)/INVALIDVAL
```
Example:
```
HOME=/home/user
GRAPHICS_INSTALL_DIR=$(HOME)/OMAP35x_Android_Graphics_SDK_3_01_00_03
ANDROID_ROOT=$(HOME)/rowboat-android/out/target/product/omap3evm/android_rootfs
CSTOOL_DIR=$(HOME)/rowboat-android/prebuilt/linux-x86/toolchain/arm-eabi-4.4.0/
KERNEL_INSTALL_DIR=$(HOME)/rowboat-android/kernel
```

### Build the Kernel Module sources ###

Execute the make command in the sources directory
```
root@android-pc:~/OMAP35x_Android_Graphics_SDK_3_01_00_03# make
```
Will display:
```
copying the sgx kernel modules to appropriate folder...
```

### Install the drivers in Target filesystem ###

Install the SGX drivers in the root filesystem built

Do a make to identify the processor option.
```
root@android-pc:~/OMAP35x_Android_Graphics_SDK_3_01_00_03# make help_km

Usage (for build): make
Usage (for install): make install OMAPES={2.x | 3.x | 5.x}
See online Graphics Getting Started Guide for further details.
```
Select the OMAP3 device you work with
```
For OMAP3530 ES 1or2 = 2.x
For OMAP3530 ES3.0   = 3.x
For AM3517           = 3.x
For AM37x           = 5.x
```
Execute the following to install the libraries and drivers into the target filesystem. Example for OMAP3530 ES 3.0:
```
root@android-pc:~/OMAP35x_Android_Graphics_SDK_3_01_00_03# make install OMAPES=3.x
```
Will display
```
Installation complete!
```


Edit init.rc to auto-start SGX service while android starts.
```
$ vim out/target/product/omap3evm/android_rootfs/init.rc
```
Just above the comment
```
# adbd is controlled by the persist.service.adb.enable system property
```
Add below two lines
```
# Start PowerVR SGX DDK
service pvr /system/bin/sgx/rc.pvr start
```

### Populate the root filesystem ###
Follow the steps below to populate the Android File system.
```
$ sudo ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2
```

The rootfs.tar.bz2 is the android filesystem, it can be put on a SD/MMC Card or used our NFS.


## Prepare SD card ##
To boot Android from SD/MMC card you must have it partitioned like following:
```
$ sudo fdisk /dev/<your sdcard,for example sdd>
Command (m for help): p

Disk /dev/sdd: 1977 MB, 1977614336 bytes
64 heads, 63 sectors/track, 957 cylinders
Units = cylinders of 4032 * 512 = 2064384 bytes
Disk identifier: 0x00000000

Device    Boot      Start         End      Blocks   Id  System
/dev/sdd1   *           1          30       60448+   b  W95 FAT32
/dev/sdd2              31         957     1868832   83  Linux
```
Format the partitions for use the commands:
```
$ sudo mkfs.msdos /dev/sdd1
$ sudo mkfs.ext3 /dev/sdd2
```
Use the kernel image (e.g. uImage) from the build process above and place in on the W95 FAT32 partition:
```
$ sudo mkdir -p /mnt/fat32
$ sudo mount /dev/sdd1 /mnt/fat32
$ sudo cp kernel/arch/arm/boot/uImage /mnt/fat32
```
Unpack the Android tarball above onto the EXT3 partition:
```
$ sudo mkdir -p /mnt/ext3
$ sudo mount /dev/sdd2 /mnt/ext3
$ sudo tar jxfv out/target/product/<board>/rootfs.tar.bz2 --numeric-owner -C /mnt/ext3
```
And the final step:
```
$ sync
$ sudo umount /mnt/fat32; sudo umount /mnt/ext3
```

## Configure target board ##
There is one important requirement:
  * for [OMAP3EVM](OMAP3EVM.md) U-Boot should be 2008.10 (Jun 16 2009-18:54:50)
  * for BeagleBoard and IGEPv2 U-Boot should be 2009.08
### Beagleboard and IGEPv2 ###
```
Beagleboard# setenv bootcmd 'mmcinit; fatload mmc 0 84000000 uImage; bootm 84000000'
Beagleboard# setenv bootargs 'mem=128M androidboot.console=ttyS2 console=tty0 console=ttyS2,115200n8 root=/dev/mmcblk0p2 rw 
init=/init rootwait omapfb.video_mode=640x480MR-16@60'
Beagleboard# saveenv
Beagleboard# reset 
```
### OMAP3\_EVM ###
This is example. Adjust to your network parameters if needed
```
OMAP3_EVM# setenv bootcmd 'tftp; bootm'
OMAP3_EVM# setenv baudrate 115200
OMAP3_EVM# setenv bootargs 'mem=128M console=tty0 console=ttyS0,115200n8 androidboot.console=ttyS0 root=/dev/mmcblk0p2 rw init=/init rootwait'
OMAP3_EVM# setenv ipaddr 10.10.10.3
OMAP3_EVM# setenv netmask 255.255.255.0
OMAP3_EVM# setenv serverip 10.10.10.1
OMAP3_EVM# setenv ethaddr 00:50:c2:XX:XX:XX
OMAP3_EVM# setenv bootfile 'evm/uImage' <-- full path on host is /tftpboot/evm/uImage
OMAP3_EVM# saveenv
OMAP3_EVM# reset
```
#### External Display ####
If you need external display you need to enable additional kernel parameters:
  * omap-dss.def\_disp=dvi - this specifies to use DVI output instead on-board screen
  * omapfb.video\_mode=720x480MR-16@60 - this specifies the resolution for external display
Here 720x480MR-16@60 is example value which means:
  * resolution - 720x480
  * M - always present
  * R - reduced blinking
  * 16 - 16 bits = 65,536 colours
  * @60 - 60 Hz
For more detailed info see [modedb Documentation](http://www.mjmwired.net/kernel/Documentation/fb/modedb.txt)

So full `bootargs` string with example values will be following:
```
OMAP3_EVM# setenv bootargs "mem=128M console=tty0 console=ttyS0,115200n8 androidboot.console=ttyS0 root=/dev/mmcblk0p2 rw 
init=/init rootwait omap-dss.def_disp=dvi omapfb.video_mode=720x480MR-16@60"
```
Remember what if you are using external display on-board display is off, but touch surface still functional.
## First start ##
Start the target board and wait until Android installing all prebuilt applications (default and custom) and configuring default settings. There is no indication for this time so you just need to wait until Android fully launched. After this you need to reboot the board.