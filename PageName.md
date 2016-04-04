# Introduction #

The DM3730 has a powerful C64x+ DSP core embedded in the SoC. This DSP core can be used for various purposes, including multimedia decoding/encoding. This offloads the host ARM processor for other general processing tasks and enables decoding of higher bitrate and/or higher resolution video and audio than would otherwise be achievable with Android running on ARM only.

[Rowboat](http://code.google.com/p/rowboat/) DSP support is based on the TI Linux Digital Video Software Development Kit (http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/latest/index_FDS.html DVSDK) for the DM3730.

This page contains information about enabling hardware acceleration for multimedia features of Android on DM3730.


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
 $ repo init -u repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-gingerbread-dsp.xml
 $ repo sync
```

**Download TI DVSDK**

Manually download the TI DVSDK package "dvsdk\_dm3730-evm\_04\_03\_00\_06\_setuplinux" to the external/ti-dsp folder from the table in web page http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/latest/index_FDS.html. Registration might be needed.

```
 $ cp dvsdk_dm3730-evm_04_03_00_06_setuplinux ~/rowboat-android/external/ti/dsp
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
 $ make CROSS_COMPILE=arm-eabi- omap3evm_config
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
 $ make CROSS_COMPILE=arm-eabi- omap3_evm_config
 $ make CROSS_COMPILE=arm-eabi- 
```

This command will generate the u-boot Image "u-boot.bin"

## Build Android, Kernel and SGX ##

```
  $ make TARGET_PRODUCT=omap3evm OMAPES=5.x
```

This command will build kernel, android sources and sgx. If you want to build these components separately, follow below section.

## Create Root Filesystem Tarball ##

Prepare the root filesystem as follows:

```
  $ cd <android source>/out/target/product/omap3evm
  $ mkdir android_rootfs
  $ cp -r root/* android_rootfs
  $ cp -r system android_rootfs
  $ sudo ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2
```

# Creating an SD Card Image #

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.

## Configure Boot Arguments ##

DM37x EVM boot arguments:

```
 setenv bootcmd 'mmc init; fatload mmc 0 80800000 uImage; bootm 80800000'
 setenv bootargs 'mem=68M@0x80000000 mem=128M@0x88000000 console=tty0 \ console=ttyO0,115200n8 androidboot.console=ttyO0 root=/dev/mmcblk0p2 rw rootfstype=ext3 init=/init rootwait ip=off mpurate=1000 vram=32M omapfb.vram=0:16M'

```

Generate the boot script (boot.scr) as follows

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

Insert your SD card into your DM37x EVM, connect serial cable to board and turn on the board. Android should boot.

Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts. You can connect mouse to board and browse UI.

- Pankaj Bharadiya (arowboat.org)