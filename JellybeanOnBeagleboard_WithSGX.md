

# Introduction #

This document explains how to download, build and run Android 4.1.1 Jelly Bean with SGX (3D graphics acceleration) support on TI's ARM Cortex A8 based AM37x Beagleboard-XM.

# Out of the Box Demo #

This section gives the instructions to quickly prepare an SD Card image and get an experience of Android Jelly Bean on Beagleboard XM.

  * Download the pre-built Image

```
 Using Curl
 $ cd ~
prebuilt images for Beagleboard
 $ curl http://rowboat.googlecode.com/files/beagleboard-xm-jb.tar.gz > beagleboard-xm-jb.tar.gz
 $ tar -zxvf beagleboard-xm-jb.tar.gz
```

Direct Download: http://rowboat.googlecode.com/files/beagleboard-xm-jb.tar.gz



  * Get a micro-SD Card of minimum size 4 GBytes (Class4 minimum) and a USB Card reader

  * Insert the USB SD Card reader (with SD Card) in your host Linux PC

  * Prepare the MMC/SD card Image

```
 $ cd ~/beagleboard_xm-jb
 $ sudo ./mkmmc-android.sh <Your SD card device e.g:/dev/sdc>
```

  * Setup the board
    1. Insert the SD Card into the Board
    1. Connect DVI monitor
    1. Switch on the board
    1. Wait for ~50 sec to get Android up on the UI screen

Note: First boot may take ~2-3 minutes.

# Getting Source Code #

## Host Setup ##

Rowboat Jelly Bean sources can be built only on 64-bit hosts. Build is tested on Ubuntu 10.10 (64-bit) and 12.04 (64-bit). Please follow the instructions at http://source.android.com/source/initializing.html to setup your build host.

### Java JDK setup ###

Ubuntu now comes only with OpenJDK which does not work for building the Rowboat Android Jelly Bean sources. Please follow the following steps to setup the Sun JDK:

Download the Sun Java JDK from the following link: http://www.oracle.com/technetwork/java/javase/downloads/jdk6-downloads-1637591.html
You need to download the Linux x64 bin installer. (Not the rpm installer)


Install the JDK into your home using the following commands:

```
  $ cp jdk-6u33-linux-x64.bin ~/
  $ cd ~
  $ chmod a+x jdk-6u33-linux-x64.bin
  $ ./jdk-6u33-linux-x64.bin
```

If prompted, accept the licence agreement by entering "yes" at the prompt. JDK will be installed to ~/jdk1.6.0\_33

Add the JDK to your PATH as follows:
```
  $export PATH="$HOME/jdk1.6.0_33/bin:$PATH"
```

### Installing Repo ###

To install, initialize, and configure repo, follow these steps

Make sure you have a bin/ directory in your home directory, and that it is included in your path

```
 $ mkdir ~/bin
 $ PATH=~/bin:$PATH
```

Download the repo script and ensure it is executable

```
 $ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo
 $ chmod a+x ~/bin/repo
```

### Initializing repo Client ###

After installing repo, set up your client to access the android source repository

Create an empty directory to hold your working files

```
 $ mkdir ~/rowboat-android
 $ cd ~/rowboat-android
```

### Get the Files ###

```
 $ repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-jb-am37x.xml (for beagleboard-xm)
 $ repo sync
```

### Download Tools ###

Download SD card utility scripts, SignGP and boot script tools & untar them into your home directory.

```
  $ cd ~
  $ curl http://rowboat.googlecode.com/files/RowboatTools.tar.gz >  RowboatTools.tar.gz
  $ tar -zxvf RowboatTools.tar.gz
```

# Compilation Procedure #

## Toolchain Setup ##

Setup the tool-chain path to point to arm-eabi- tools in prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin/

```
  $ export PATH=<android source>/rowboat-android/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin:$PATH
```

## Build x-loader ##

Change directory to x-loader

```
  $ cd x-loader
```

Execute the following commands

```
 $ make CROSS_COMPILE=arm-eabi- distclean
 $ make CROSS_COMPILE=arm-eabi- omap3beagle_config
 $ make CROSS_COMPILE=arm-eabi-
```

This command will build the x-loader Image "x-load.bin"

To create the MLO file used for booting from a MMC/SD card, sign the x-loader image using the signGP tool.

```
  $ ./signGP ./x-load.bin
```

Note: You will need to copy the signGP tool from the Tools/signGP directory to the directory that contains the x-load.bin file

The signGP tool will create a .ift file, rename the x-load.bin.ift to MLO

```
  $ mv x-load.bin.ift MLO
```

## Build Boot Loader (u-boot) ##

Change directory to u-boot

```
  $ cd u-boot
```

Execute following commands

```
  $ make CROSS_COMPILE=arm-eabi- distclean
  $ make CROSS_COMPILE=arm-eabi- omap3_beagle_config
  $ make CROSS_COMPILE=arm-eabi- 
```

This command will generate the u-boot Image "u-boot.bin"
Build Android, Kernel and SGX

```
  $ make TARGET_PRODUCT=beagleboard OMAPES=5.x
```

This command will build kernel, android sources and sgx. If you want to build these components separately, follow below section.
Build Android, Kernel and SGX Separately

## Build Kernel ##

Change directory to kernel

```
 $ cd kernel
```

Build kernel

```
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- distclean
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- omap3_beagle_android_defconfig
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- uImage
```

This will generate uImage (kernel image) in kernel/arch/arm/boot folder
Build Android filesystem

## Build Root Filesystem ##

Use following commands to build the root file system.

```
  $ cd <Android source path>
  $ make TARGET_PRODUCT=beagleboard droid
```

Build and Install SGX(2D/3D accleration) drivers and libraries

Use following commands to build and install SGX
Prepare the root filesystem as follows:

> $ cd <android source>
> $ make TARGET\_PRODUCT=beagleboard fs\_tarball

# Creating an SD Card Image #

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.

## Configure Boot Arguments ##

Beagleboard:

The default boot arguments are located at "~/RowboatTools-JB/am37x/mk-bootscr/boot.scr" boot script. If you want to make changes to the boot arguments, you need to re-generate the boot script (boot.scr) as follows

Change directory to mk-bootscr folder.

```
 $ cd ~/RowboatTools-JB/am37x/mk-bootscr
```

Edit mkbootscr file as per your boot arguments.

Generate boot.scr boot script

```
 $ ./mkbootscr
```

# Flash SD Card #

Copy compiled Images to image folder and create a bootable SD card as follows.

```
  $ mkdir ~/image_folder
  $ cp <android sorce path>/kernel/arch/arm/boot/uImage ~/image_folder
  $ cp <android souorce path>/u-boot/u-boot.bin ~/image_folder
  $ cp <android source path>/x-loader/MLO ~/image_folder
  $ cp ~/RowboatTools-JB/am37x/mk-bootscr/boot.scr ~/image_folder
  $ cp <android source path>/out/target/product/beagleboard /rootfs.tar.bz2 ~/image_folder
  $ cp ~/RowboatTools-JB/am37x/mk-mmc/mkmmc-android.sh ~/image_folder

  $cd ~/image_folder
  $ sudo./mkmmc-android <your SD card device e.g.:/dev/sdc> MLO u-boot.bin uImage boot.scr rootfs.tar.bz2
```

# Booting Up the Board #

Insert your SD card into your beagleboard, connect serial cable, DVI display to board and turn on the board.

Android Jelly Bean should boot.

Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts. You can connect mouse to the board and browse the Jelly Bean UI.

Power Management is not fully integrated with android. We suggest to enable the "Keep Awake" setting in Settings-> Developer Options menu.

Enjoy the new dessert from Rowboat - Vishveshwar Bhat and Shakti Tiwari