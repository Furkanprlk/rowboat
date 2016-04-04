



# Introduction #

This document explains how to download, build and run Android 4.2.2 Jelly Bean with SGX (3D graphics acceleration) support on TI's ARM Cortex A8 based AM335x Beaglebone (Rev A3 and later) with LCD7 cape

# Out of the Box Demo #
> _NOTE:_ **The demo images are based on Android Jelly Bean 4.1.1. Updated images with Jelly Bean 4.2.2 are not available currently and needs to be built form source**

This section gives the instructions to quickly prepare an SD Card image and get an experience of Android Jelly Bean on Beaglebone.


  * Download the pre-built Image

```
  Using Curl
  $ cd ~
  $ curl http://rowboat.googlecode.com/files/beaglebone-jb.tar.gz > beaglebone-jb.tar.gz
  $ tar -zxvf beaglebone-jb.tar.gz
```

Direct Download:  http://rowboat.googlecode.com/files/beaglebone-jb.tar.gz


  * Get a micro-SD Card of minimum size 4 GBytes (Class4 minimum) and a USB Card reader

  * Insert the USB SD Card reader (with SD Card) in your host Linux PC

  * Prepare the MMC/SD card Image

```
  $ cd ~/beaglebone-jb
  $ sudo ./mkmmc-android.sh <Your SD card device e.g:/dev/sdc>
```

  * Setup the board
    1. Insert the SD Card into the Board
    1. Switch on the board
    1. Wait for ~50 sec to get Android up on the UI screen

Note: First boot may take ~2-3 minutes.

## Getting serial console ##

Serial console is provided via mini-usb cable connection between the Beaglebone and the Host.To establish a connection the serial console enter the following commands on the linux console

```
  $ sudo modprobe ftdi_sio vendor=0x0403 product=0xa6d0
  $ minicom -D /dev/`dmesg | grep FTDI | grep "now attached to" | tail -n 1 | awk '{ print $NF }'` 
```

# Getting Source Code #

## Host Setup ##

Rowboat Jelly Bean sources can be built **ONLY** on 64-bit hosts. Build is tested on Ubuntu 10.10 (64-bit) and 12.04 (64-bit). Please follow the instructions at http://source.android.com/source/initializing.html to setup your build host.

You also need to install the following packages in addition: minicom tftpd uboot-mkimage expect

```
  $ sudo apt-get install minicom tftpd uboot-mkimage expect
```
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

If prompted, accept the licence agreement by entering "yes". JDK will be installed to ~/jdk1.6.0\_33

Add the JDK to your PATH as follows:

```
  $ export PATH="$HOME/jdk1.6.0_33/bin:$PATH"
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
  $ repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-jb-am335x.xml
  $ repo sync $
```

# Compilation Procedure #

## Toolchain Setup ##

Setup the tool-chain path to point to arm-eabi- tools in prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin/

```
  $ export PATH=<android source>/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin:$PATH
```

## Build Boot Loader (MLO/u-boot) ##

Change directory to u-boot

```
  $ cd u-boot
```

Execute following commands

```
  $ make CROSS_COMPILE=arm-eabi- distclean
  $ make CROSS_COMPILE=arm-eabi- am335x_evm_config
  $ make CROSS_COMPILE=arm-eabi- 
```

This command will generate the "MLO" and "u-boot.img"

## Build Android, Kernel and SGX ##

```
  $ make TARGET_PRODUCT=beaglebone OMAPES=4.x
```

This command will build kernel, android sources and sgx. If you want to build these components separately, follow below section.

## Build Android, Kernel and SGX Separately ##

### Build Kernel ###

Change directory to kernel

```
  $ cd kernel
```

Build kernel

```
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- distclean
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- am335x_evm_android_defconfig
  $ make ARCH=arm CROSS_COMPILE=arm-eabi- uImage
```

This will generate uImage (kernel image) in kernel/arch/arm/boot folder
Build Android filesystem

### Build Root Filesystem ###

Use following commands to build the root file system.

```
  $ cd <Android source path>
  $ make TARGET_PRODUCT=beaglebone droid
```

### Build and Install SGX(2D/3D accleration) drivers and libraries ###

Use following commands to build and install SGX.

```
  $ cd <Android source path>/hardware/ti/sgx
  $ make TARGET_PRODUCT=beaglebone OMAPES=4.x ANDROID_ROOT_DIR=<android source path>
  $ make TARGET_PRODUCT=beaglebone OMAPES=4.x ANDROID_ROOT_DIR=<android source path> install
```

# Create Root Filesystem Tarball #

Prepare the root filesystem as follows:

```
  $ cd <android source>
  $ make TARGET_PRODUCT=beaglebone fs_tarball
```

# Creating an SD Card Image #

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.

## Configure Boot Arguments ##

The default boot arguments are located in the "<android source>/external/ti\_android\_utilities/am335x/u-boot-env/uEnv\_beaglebone.txt" boot script. You can edit this file to update the boot commands.


## Flash SD Card ##

Copy compiled Images to image folder and create a bootable SD card as follows.

```
  $ mkdir ~/image_folder
  $ cp <android source>/kernel/arch/arm/boot/uImage ~/image_folder
  $ cp <android source>/u-boot/MLO ~/image_folder
  $ cp <android source>/u-boot/u-boot.img ~/image_folder
  $ cp <android source>/external/ti_android_utilities/ti_android_utilities/am335x/u-boot-env/uEnv_beaglebone.txt ~/image_folder
  $ cp <android source>/out/target/product/beaglebone/rootfs.tar.bz2 ~/image_folder
  $ cp <android source>/external/ti_android_utilities/am335x/mk-mmc/mkmmc-android.sh ~/image_folder

  $ cd ~/image_folder
  $ sudo./mkmmc-android.sh <your SD card device e.g.:/dev/sdc> MLO u-boot.img uImage uEnv_beaglebone.txt rootfs.tar.bz2
```

# BeagleBone Black #
**Detailed Instructions soon!**

Replace TARGET\_PRODUCT=beaglebone with **TARGET\_PRODUCT=beagleboneblack** in the above build instructions and use the file **<android source>/external/ti\_android\_utilities/ti\_android\_utilities/am335x/u-boot-env/uEnv\_beagleboneblack.txt** as the uEnv.txt file.

# Booting Up the Board #

Insert your SD card into your Beaglebone, connect mini-USB cable and, optionally USB Mouse to board and turn on the board.

Android Jelly Bean should boot.

Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts. You can browse the Jelly Bean UI using the touchscreen or by connecting a USB mouse to the Beaglebone..

Power Management is not fully integrated with android. We suggest to enable the "Keep Awake" setting in Settings-> Developer Options menu.

Enjoy new dessert from Rowboat -- Vishveshwar Bhat and Shakti Tiwari.