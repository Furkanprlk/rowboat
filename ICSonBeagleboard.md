# Introduction #

This document explains how to build and run Android 4.0 Ice Cream Sandwich on TI's AM37x (ARM Cortex A8) based BeagleboardXM.

NOTE: This activity is under progress can be used for evaluation purposes only.

# Out of the Box Demo #

This section gives the instructions to quickly prepare an SD Card image and get an experience of Android Ice Cream Sandwich on Beagleboard XM.

**Download the pre-built Image**

```
 Using Curl
 $ cd ~
 $ curl http://rowboat.googlecode.com/files/beagleboard-xm.tar.gz > beagleboard-xm.tar.gz
 $ tar -zxvf beagleboard_xm.tar.gz

 Direct Download
 http://rowboat.googlecode.com/files/beagleboard-xm.tar.gz 
$ tar -zxvf beagleboard_xm.tar.gz

```

**Get an SD Card of minimum size 4GBytes (Class4 minimum) and a USB Card reader**

**Insert the USB SD Card reader (with SD Card) in your host Linux PC**

**Prepare the MMC/SD card Image**

```
 $ cd ~/beagleboard_xm
 $ sudo ./mkmmc-android.sh <Your SD card device e.g:/dev/sdc>
```

**Setup the board**

```
1. Insert the SD Card into the Board
2. Connect DVI monitor
3. Switch on the board
4. Wait for ~40 sec to get Android up on the UI screen 

```

# Getting Source Code #

## Installing Repo ##

To install, initialize, and configure repo, follow these steps

**Make sure you have a bin/ directory in your home directory, and that it is included in your path**

```
 $ mkdir ~/bin
 $ PATH=~/bin:$PATH
```

**Download the repo script and ensure it is executable**

```
 $ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo
 $ chmod a+x ~/bin/repo
```

## Initializing repo Client ##

After installing repo, set up your client to access the android source repository

**Create an empty directory to hold your working files**

```
 $ mkdir ~/rowboat-android
 $ cd ~/rowboat-android
```

**Get the Files.**

```
 $ repo init -u https://android.googlesource.com/platform/manifest -b master
 $ repo sync
```

## Clone x-loader ##

Use following commands to clone x-loader

```
 $ git clone git://gitorious.org/rowboat/x-loader.git
 $ git checkout -b OMAPPSP_04.02.00.07 remotes/rowboat/OMAPPSP_04.02.00.07
```

## Clone u-boot ##

Use following commands to clone u-boot

```
 $ git clone git://gitorious.org/rowboat/u-boot.git
 $ git checkout -b OMAPPSP_04.02.00.07 remotes/rowboat/OMAPPSP_04.02.00.07

```

## Clone Kernel ##

Use following commands to clone kernel

```
 $ git clone git://gitorious.org/rowboat/kernel.git
 $ git checkout -b rowboat-kernel-2.6.37 remotes/rowboat/rowboat-kernel-2.6.37

```

# Download Beagleboard ICS patches #

Download beagleboard ICS patches

```
 $ cd ~
 $ curl http://rowboat.googlecode.com/files/Beagleboard_ICS_patches.tar.gz > Beagleboard_ICS_patches.tar.gz
 $ tar -zxvf Beagleboard_ICS_patches.tar.gz
```

The patches are organized as shown below:
```
Beagleboard_ICS_patches
.
|-- bionic
|   `-- 0001-Add-omapfb-header-file.patch
|-- device
|   `-- ti
|       `-- beagleboard
|           `-- 0001-Initial-configuration-for-beagleboard.patch
|-- frameworks
|   `-- base
|       |-- 0001-change-default-device-type-for-touch-screen.patch
|       `-- 0002-disble-hardware-renderer.patch
`-- hardware
    |-- libhardware
    |   `-- 0001-call-OMAPFB_WAITFORGO-ioctl-to-wait-for-empty-buffer.patch
    `-- ti
        `-- omap3
            `-- 0001-Add-LOCAL_MODULE_TAGS-to-fix-build-error.patch
```

### Apply patches to Android ICS sources ###

bionic
```
 $ cd <android source path>/bionic
 $ git am <patch_path>/001-Add-omapfb-header-file.patch
```

device/ti/beagleboard

In device/ti create a new folder named beagleboard and initialize new GIT repository

```
 $ mkdir <android source path>/device/ti/beagleboard
 $ cd <android source path>/device/ti/beagleboard
 $ git init .
 $ git am 0001-Initial-configuration-for-beagleboard.patch
```

frameworks/base
```
 $ cd <android source path>/frameworks/base
 $ git am <patch_path>/0001-change-default-device-type-for-touch-screen.patch
 $ git am <patch_path>/0002-disble-hardware-renderer.patch
```

hardware/libhardware
```
 $ cd <android source path>/hardware/libhardware
 $ git am <patch_path>/0001-call-OMAPFB_WAITFORGO-ioctl-to-wait-for-empty-buffer.patch
```

hardware/ti/omap3
```
 $ cd <android source path>/hardware/ti/omap3
 $ git am <patch_path>/0001-Add-LOCAL_MODULE_TAGS-to-fix-build-error.patch
```

# Download Tools #

Download SD card utility scripts, SignGP and boot script tools & untar them into your home directory.

```
cd ~
curl http://rowboat.googlecode.com/files/RowboatTools.tar.gz > RowboatTools.tar.gz
tar -zxvf RowboatTools.tar.gz
```

# Compilation Procedure #

## Toolchain Setup ##

Setup the tool-chain path to point to arm-eabi- tools in prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin

```
 $ export PATH=<android source>/rowboat-android/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin:$PATH
```

## Build x-loader ##

**Change directory to x-loader**

```
 $ cd x-loader
```

**Execute the following commands**

```
 $ make CROSS_COMPILE=arm-eabi- distclean
 $ make CROSS_COMPILE=arm-eabi- omap3beagle_config
 $ make CROSS_COMPILE=arm-eabi-
```

This command will build the x-loader Image "x-load.bin"

To create the MLO file used for booting from a MMC/SD card, sign the x-loader image using the signGP tool.

```
  $ ./signGP ./x-load.bin

 Note: You will need to copy the signGP tool from the Tools/signGP directory to the directory that contains the x-load.bin file
```

The signGP tool will create a .ift file, rename the x-load.bin.ift to MLO

```
 $ mv x-load.bin.ift MLO
```

## Build Boot Loader (u-boot) ##

**Change directory to u-boot**

```
 $ cd u-boot
```

**Execute following commands**

```
 $ make CROSS_COMPILE=arm-eabi- distclean
 $ make CROSS_COMPILE=arm-eabi- omap3_beagle_config
 $ make CROSS_COMPILE=arm-eabi- 
```

This command will generate the u-boot Image "u-boot.bin"

## Build Kernel ##

**Change directory to kernel**

```
 $ cd kernel
```

**Build kernel**

```
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- distclean
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- omap3_beagle_android_defconfig
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- uImage
```

This will generate uImage (kernel image) in kernel/arch/arm/boot folder

**Build Android filesystem**

Use following commands to build the root file system.

```
  $ cd <Android source path>
  $ make TARGET_PRODUCT=beagleboard TARGET_BUILD_TYPE=release
```

## Create Root Filesystem Tarball ##

Prepare the root filesystem as follows:

```
  $ cd <android source>/out/target/product/beagleboard
  $ mkdir android_rootfs
  $ cp -r root/* android_rootfs
  $ cp -r system android_rootfs
  $ sudo ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2
```

# Creating an SD Card Image #

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.

## Configure Boot Arguments ##

The default boot arguments are located at "~/RowboatTools/mk-bootscr/boot.scr" boot script. If you want to make changes to the boot arguments, you need to re-generate the boot script (boot.scr) as follows

**Change directory to mk-bootscr folder.**

```
 $ cd ~/RowboatTools/mk-bootscr
```

**Edit mkbootscr file as per your boot arguments.**

**Generate boot.scr boot script
```
 $ ./mkbootscr
```**

## Flash SD Card ##

Copy compiled Images to image folder and create a bootable SD card as follows.

```
 $ mkdir ~/image_folder
 $ cp <android sorce path>/kernel/arch/arm/boot/uImage ~/image_folder
 $ cp <android souorce path>/u-boot/u-boot.bin ~/image_folder
 $ cp <android source path>/x-loader/MLO ~/image_folder
 $ cp ~/RowboatTools/mk-bootscr/boot.scr ~/image_folder
 $ cp <android source path>/out/target/product/beagleboard/rootfs.tar.bz2 ~/image_folder
 $ cp ~/RowboatTools/mk-mmc/mkmmc-android.sh ~/image_folder

 $cd ~/image_folder
 $ sudo./mkmmc-android <your SD card device e.g.:/dev/sdc> MLO u-boot.bin uImage boot.scr rootfs.tar.bz2
```

## Booting Up the Board ##

Insert your SD card into your beagleboard, connect serial cable, DVI display to board and turn on the board.

Android ICS should boot.

Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts. You can connect mouse to board and browse ICS UI.

Enjoy the all new desert with beagle - Pankaj Bharadiya (arowboat.org)