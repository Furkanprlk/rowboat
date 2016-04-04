# Introduction #

This document explains how to download, build and run Android 4.0 Ice Cream Sandwich on TI's AM37x (ARM Cortex A8) based BeagleboardXM with SGX (3D graphics acceleration) support


# Out of the Box Demo #

This section gives the instructions to quickly prepare an SD Card image and get an experience of Android Ice Cream Sandwich on Beagleboard XM.

**Download the pre-built Image**

```
 Using Curl
 $ cd ~
 $ curl http://rowboat.googlecode.com/files/beagleboard_xm_sgx.tar.gz > beagleboard_xm_sgx.tar.gz
 $ tar -zxvf beagleboard_xm_sgx.tar.gz

 Direct Download
 http://rowboat.googlecode.com/files/beagleboard_xm_sgx.tar.gz 
$ tar -zxvf beagleboard_xm_sgx.tar.gz

```

**Get an SD Card of minimum size 4 GBytes (Class4 minimum) and a USB Card reader**

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
 $ repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-ics.xml
 $ repo sync
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

## Build Android, Kernel and SGX ##

```
  $ make TARGET_PRODUCT=beagleboard OMAPES=5.x
```

This command will build kernel, android sources and sgx. If you want to build these components separately, follow below section.

## Build Android, Kernel and SGX Separately ##

### Build Kernel ###

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

### Build Android filesystem ###

Use following commands to build the root file system.

```
  $ cd <Android source path>
  $ make TARGET_PRODUCT=beagleboard
```

### Build and Install SGX(2D/3D accleration) drivers and libraries ###

Use following commands to build the root file system.

```
  $ cd <Android source path>/hardware/ti/sgx
  $ make TARGET_PRODUCT=beagleboard OMAPES=5.x ANDROID_ROOT_DIR=<android source path>
  $ make TARGET_PRODUCT=beagleboard OMAPES=5.x ANDROID_ROOT_DIR=<android source path> install
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