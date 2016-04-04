# Introduction #

This document explains how to build and run Android Gingerbread on TI816x and TI814x with support for hardware-accelerated multimedia.

These instructions have been tested with a fresh installation of Ubuntu 10.04 LTS.  Some steps might be slightly different for other Linux distributions or different Ubuntu versions.

# Initializing Your Build Environment #

Many of the steps in this section are based on instructions in this document:  http://source.android.com/source/initializing.html

## Installing Needed Ubuntu Packages ##

For a 64-bit Ubuntu installation, run the following commands:

```
sudo apt-get update
sudo apt-get install python-software-properties git-core gnupg flex bison \
  gperf build-essential zip curl zlib1g-dev libc6-dev lib32ncurses5-dev   \
  ia32-libs x11proto-core-dev libx11-dev lib32readline5-dev lib32z1-dev   \
  libgl1-mesa-dev g++-multilib mingw32 tofrodos corkscrew expect uboot-mkimage
```

For a 32-bit Ubuntu installation, run the following commands:

```
sudo apt-get update
sudo apt-get install python-software-properties git-core gnupg flex bison     \
  gperf build-essential zip curl zlib1g-dev libc6-dev libncurses5-dev         \
  x11proto-core-dev libx11-dev libreadline5-dev libgl1-mesa-dev  g++-multilib \
  mingw32 tofrodos corkscrew expect uboot-mkimage
```


## Installing the JDK ##

Run the following commands:

```
sudo add-apt-repository "deb http://archive.canonical.com/ lucid partner"
sudo add-apt-repository "deb-src http://archive.canonical.com/ubuntu lucid partner"
sudo apt-get update
sudo apt-get install sun-java6-jdk
```

# Downloading the Source #

Many of the steps in this section are based on instructions in this document:  http://source.android.com/source/downloading.html

## Proxy Settings ##

If you are behind a proxy server, you need to run the following commands to access the external rowboat repositories.  Replace `<http proxy server>:<port>` with the name of your http proxy server and proxy port, and configure your https and ftp proxy servers in a similar fashion.

```
export http_proxy=<http proxy server>:<port>
export https_proxy=<https proxy server>:<port>
export ftp_proxy=<ftp proxy server>:<port>

cat > ~/git-proxy.sh <<END
#!/bin/sh

CS=/usr/bin/corkscrew
PROXY_HOST=<http proxy server>
PROXY_PORT=<port>
exec \${CS} \${PROXY_HOST} \${PROXY_PORT} \$*
END

chmod a+x ~/git-proxy.sh
git config --global core.gitproxy "${HOME}/git-proxy.sh"
```

## Install Repo ##

Run the following commands:

```
mkdir ~/bin
PATH=~/bin:$PATH
curl https://android.git.kernel.org/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

## Download the Source ##

Before proceeding, first decide if you want to use a known-good snapshot of the rowboat source that was tested with these instructions, or if you want the bleeding-edge source.  If you're new to Android, please start with the known-good snapshot to make sure the initial task of getting up and running goes as smoothly as possible.

Please don't report problems with these build instructions without first trying the known-good snapshot.

_**Known-Good Snapshot:**_

Run the following commands to download the version of the Android source code that was tested with these instructions:

```
mkdir ~/rowboat-android
cd ~/rowboat-android
repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-gingerbread-ti81xx-mc-dsp-snapshot-GMT20110926200120.xml
repo sync
```

_**Bleeding-Edge Source:**_

Run the following commands to download the latest version Android source code, which may have been updated since these instructions were written:

```
mkdir ~/rowboat-android
cd ~/rowboat-android
repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-gingerbread-ti81xx-mc-dsp.xml
repo sync
```

_**Install the TI SDK Components:**_

Install the TI SDK components needed for multimedia acceleration.  If these are not installed prior to building Android, the Android build will fail.

```
cd ~/rowboat-android/hardware/ti/ti81xx
stty cols 80
./install_mc_dsp_components.sh
```

# Build Android #

To build Android the command below for the desired platform.  Android and the Linux kernel will be built, as well as the SGX drivers and other kernel modules that are needed for communication with hardware accelerators and codecs.

_**For TI816x:**_
```
cd ~/rowboat-android
make TARGET_PRODUCT=ti816xevm OMAPES=6.x
make TARGET_PRODUCT=ti816xevm OMAPES=6.x fs_tarball
```

_**For TI814x:**_
```
cd ~/rowboat-android
make TARGET_PRODUCT=ti814xevm OMAPES=6.x
make TARGET_PRODUCT=ti814xevm OMAPES=6.x fs_tarball
```

On 32-bit systems you will see the following warning:

```
build/core/main.mk:79: ************************************************************
build/core/main.mk:80: You are attempting to build on a 32-bit system.
build/core/main.mk:81: Only 64-bit build environments are supported beyond froyo/2.2.
build/core/main.mk:82: ************************************************************
```

It is ok to ignore this warning, as rowboat has patched Android to build on 32-bit machines.

The Android source base is large and the build will take awhile to complete depending on the speed of your machine.

# Creating an SD Card Image #

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.

## Download and Install the SD Card Utilities ##

To create an SD card image containing your Android build, first download the SD card utility scripts and untar them into your home directory.

```
cd ~/
wget http://rowboat.googlecode.com/files/rowboat-sdcard-utils.tar.bz2
tar xjvf rowboat-sdcard-utils.tar.bz2
```

## Configure Boot Arguments ##

The default boot arguments are located at `~/rowboat-sdcard-utils/Boot_Images/boot.scr.ti816x.config` for TI816x and `~/rowboat-sdcard-utils/Boot_Images/boot.scr.ti814x.config` for TI814x.  Edit them as you see fit, or leave them at the default settings.

If you make changes to the boot arguments, you need to re-generate the boot image for them as follows:

_**For TI816x:**_
```
cd ~/rowboat-sdcard-utils/Boot_Images
./make_boot_scr.sh boot.scr.ti816x.config boot.scr.ti816x
```

_**For TI814x:**_
```
cd ~/rowboat-sdcard-utils/Boot_Images
./make_boot_scr.sh boot.scr.ti814x.config boot.scr.ti814x
```

## Prepare Media Clips ##

If you would like any media clips to be included on your SD card (e.g., for demo purposes), copy them into

```
~/rowboat-sdcard-utils/Media_Clips
```

For example, run these commands to include the Sintel movie trailer:

```
cd ~/
wget http://rowboat.googlecode.com/files/sintel_trailer-1080p-H264-HP-L40_AAC-2CH.tar.bz2
mkdir -p ~/rowboat-sdcard-utils/Media_Clips/Video
tar xjvf sintel_trailer-1080p-H264-HP-L40_AAC-2CH.tar.bz2 --strip-components=1 -C ~/rowboat-sdcard-utils/Media_Clips/Video
```

## Create the SD Card ##

The most critical part of creating the SD card is determining what device your SD card is.  Place an SD card into your SD card reader, and run:

```
sudo fdisk -l | egrep "^Disk /dev"
```

This will list all the hard drives and other mounted media your machine.  The output will look something like this:

```
Disk /dev/sda: 1000.2 GB, 1000204886016 bytes
Disk /dev/sdb: 1000.2 GB, 1000204886016 bytes
Disk /dev/sdc: 500.1 GB, 500107862016 bytes
Disk /dev/sdd: 1967 MB, 1967128576 bytes
```

Here I can see my two 1TB hard drives, my 500GB external USB drive, and the 2GB SD card that is in my machine.  The size of the device (2GB) is indicating to me that "/dev/sdd" is the device ID for the SD card.  Also, devices are usually assigned in-order alphabetically, so the fact that it is last in the list also shows that it is likely the most recent device inserted into the system.

We will be writing the SD card as the root user, and if you use the wrong device file name, there is nothing stopping you from overwritting a hard drive and making it unusable instead of creating an SD card.  **PROCEED WITH CAUTION**

Once you are satisfied that you know the device of your SD card, run the following commands:

_**For TI816x:**_
```
cd ~/rowboat-sdcard-utils
sudo ./mkmmc-android.sh <your SD card device> ~/rowboat-android/u-boot/MLO \
    ~/rowboat-android/u-boot/u-boot.bin                                    \
    ~/rowboat-android/kernel/arch/arm/boot/uImage                          \
    ~/rowboat-sdcard-utils/Boot_Images/boot.scr.ti816x                     \
    ~/rowboat-android/out/target/product/ti816xevm/rootfs.tar.bz2          \
    ~/rowboat-sdcard-utils/Media_Clips
```

_**For TI814x:**_
```
cd ~/rowboat-sdcard-utils
sudo ./mkmmc-android.sh <your SD card device> ~/rowboat-android/u-boot/MLO \
    ~/rowboat-android/u-boot/u-boot.bin                                    \
    ~/rowboat-android/kernel/arch/arm/boot/uImage                          \
    ~/rowboat-sdcard-utils/Boot_Images/boot.scr.ti814x                     \
    ~/rowboat-android/out/target/product/ti814xevm/rootfs.tar.bz2          \
    ~/rowboat-sdcard-utils/Media_Clips
```

## Booting the SD card ##
Insert your new SD card into your device, and make sure the DIP switch settings are configured to boot form an SD card.  Turn on the device, and Android Gingerbread should boot.  Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts the first time it is run.

If you prepared any media clips for inclusion on the SD card, they should appear in the Gallery.  Sometimes clips don't appear during the initial Android boot, and if this happens try re-booting and they should show up from that point onward.