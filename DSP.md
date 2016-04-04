
# Latest Update #
  * 05/31/2011 - rowboat-gingerbread-dsp.xml is available (for DM37x EVM and Beagleboard-xM only).
    * similar to the features in rowboat-froyo-dsp.xml, but uses Stagefright multimedia framework instead of OpenCORE;
    * Support up-to 720P MPEG4 ASP decoding @30fps @10Mbps;
    * Support up-to 720P H.264 HP Lvl3.1 decoding @24fps @4Mbps;
    * requires 1GHz DM37xx device to support 720P video decoding;
    * OMAP3530 is not supported at the moment;
  * ~~01/23/2011 - rowboat-froyo-dsp.xml updated TI DVSDK package to v4.01.00.09.~~


# Benefits #

The OMAP3530 or DM3730 has a powerful C64x+ DSP core embedded in the SoC. This DSP core can be used for various purposes, including multimedia decoding/encoding.  This offloads the host ARM processor for other general processing tasks and enables decoding of higher bitrate and/or higher resolution video and audio than would otherwise be achievable with Android running on ARM only.

[Rowboat](http://code.google.com/p/rowboat/) DSP support is based on the TI Linux Digital Video Software Development Kit ([DVSDK](http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/)) for the OMAP3530/DM3730.

# Software Stack Components #

The following components form a complete software stack needed to work efficiently with DSP. They are provided together as the TI DVSDK or they can be downoaded as individual packages directly from TI.

  1. **DSP/BIOS** (and tools)
    * A TI provided realtime operating system for DSP's
    * [DSP/BIOS information on TI E2E](http://processors.wiki.ti.com/index.php?title=Category:DSPBIOS)
    * [Wikipedia Overview of DSP/BIOS](http://en.wikipedia.org/wiki/SYS/BIOS)
  1. **Codec Engine** (and tools)
    * A TI provided framework to enable instantiation/control of Codecs running on DSP from the ARM
    * http://processors.wiki.ti.com/index.php/Category:Codec_Engine
  1. **DSP/BIOS Link**
    * TI provided driver provided low-level communications between the ARM and the DSP
    * [DSP/BIOS Link Training Material](http://focus.ti.com/docs/training/catalog/events/event.jhtml?sku=WEB108007)
  1. **DMAI**
    * The DaVinci Multimedia Application Interface (DMAI) is a thin utility layer on top of the operating system (Linux or DSP/BIOS) and the Codec Engine (CE) to assist in quickly writing portable applications on a DaVinci (or OMAP) platform.
    * http://processors.wiki.ti.com/index.php/Davinci_Multimedia_Application_Interface
  1. **OpenCore DMAI Codecs**
    * Integration of TI DSP accelerated codecs into the PacketVideo OpenCore framework that is provided here on the rowboat Android development site.
    * Technical Details available [here](http://code.google.com/p/rowboat/wiki/DSPCodecs)
  1. **additional DSP codecs** (optional), for example, mp3 DSP codec

# Building and Testing DSP stack #

This section explains how to build and run Gingerbread+DSP on DM37x devices. The next section provides the difference for Froyo+DSP on DM37x and OMAP35x devices.

## Preparation ##

  1. Rowboat is patched to be built on 32-bit Linux host only.
  1. Check if you have _git_, _java6-jdk_, _dos2unix_, and _expect_ utilities installed on your build host. Use the _which_ command from the Linux prompt to see if they commands are present (i.e. which git)
  1. Download _repo_. You can find detailed repo installing instructions [here](http://source.android.com/source/git-repo.html). Make sure ~/bin is in $PATH
```
# mkdir -p ~/bin
# curl http://android.git.kernel.org/repo >~/bin/repo
# chmod a+x ~/bin/repo
```
  1. Download Rowboat Android (Gingerbread version) with integrated TI DSP stack from gitorious.org/rowboat. Please ensure you have atleast 17GB free space on your hard drive for the source and build temporary files.
```
# mkdir ~/rowboat-android
# cd ~/rowboat-android
# repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-gingerbread-dsp.xml
# repo sync
```
  1. Manually download the TI DVSDK package to the _external/ti-dsp_ folder from the table in webpage http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/4_01_00_09/index_FDS.html. Registration might be needed.
    * For DM37xx platform, download _dvsdk\_dm3730-evm\_4\_01\_00\_09\_setuplinux_ package;
    * For OMAP35xx platform, download _dvsdk\_omap3530-evm\_4\_01\_00\_09\_setuplinux_ package.

## Build ##

If English is not the locale in your host machine, some package install scripts will fail due to unexpected non-English output. Set env _LANG_ as following to fix it:
```
# export LANG=C 
```
To build Android with TI DSP stack use following command. It builds Android and Linux kernel for the selected platform, TI kernel modules for DSP communication and codec server, and SGX drivers as while.
```
# cd ~/rowboat-android
# make TARGET_PRODUCT=[omap3evm | beagleboard] [OMAPES=5.x]
```

Your must specify your board with **TARGET\_PRODUCT** variable. **OMAPES** variable is set to 5.x by default to build for DM37xx only.

Create the rootfs image, assuming TARGET\_PRODUCT=omap3evm in the command above:
```
# cd out/target/product/omap3evm
# mkdir android_rootfs
# cp -r root/* system android_rootfs
# ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2
```

For DM37x EVM, download the [TI Gingerbread Devkit 1.0 DM37x Prebuilt Image Package](http://software-dl.ti.com/dsps/dsps_public_sw/sdo_tii/TI_Android_DevKit/TI_Android_GingerBread_2_3_DevKit_1_0/exports/DM37X.tar.gz) and untar it to get _MLO_ and _u-boot_ images, which are _DM37X/Boot\_Images/MLO_ and _DM37X/Boot\_Images/u-boot.bin_ respectively.
```
# wget http://software-dl.ti.com/dsps/dsps_public_sw/sdo_tii/TI_Android_DevKit/TI_Android_GingerBread_2_3_DevKit_1_0/exports/DM37X.tar.gz
# tar zxf DM37X.tar.gz
```

To get _MLO_ and _u-boot.bin_ for Beagleboard-xM, download the [Beagleboard-xM Gingerbread prebuilt image package](http://software-dl.ti.com/dsps/dsps_public_sw/sdo_tii/TI_Android_DevKit/TI_Android_GingerBread_2_3_DevKit_1_0/exports/beagleboard-xm.tar.gz).

Follow the instructions in [ConfigureAndBuild#Prepare\_SD\_card](http://code.google.com/p/rowboat/wiki/ConfigureAndBuild#Prepare_SD_card) to populate the rootfs to a SD card. Before copying _uImage_ to the SD card, first copy _MLO_ to the SD card first partition; also copy _u-boot.bin_ into the same partition. _MLO_ has to be copied first, otherwise the board will not boot.

[How to Make 3 Partition SD Card](http://processors.wiki.ti.com/index.php/How_to_Make_3_Partition_SD_Card) wiki is an alternative reference to populate rowboat to a SD card.

## Boot ##

DVSDK DSP stack by default uses physical memory window within the first 128MB. In order not to change DSP stack mappings we recommend to use memory hole settings as in the examples below for boards with more than 128MB DDR. (Don't forget to adjust these parameters for your board)

Boards with 128MB of memory are not tested and not recommended.

You'll probably have kernel crash issue due to v4l2 buffer allocation failure with the omap\_vout driver in some cases. To avoid this, try following _bootargs_ additions: ` omap_vout.vid1_static_vrfb_alloc=y`.

The following is an example of u-boot env setup for DM3730 EVM with 256MB DDR to boot from SD card. (It includes artificial line breaks to maintain a print-friendly document.) 'mpurate=1000' is necessary to decode 720P video clips. **Please use 1GHz verified DM37x devices, otherwise DSP will crash while running at 1GHz.**
```
OMAP3_EVM# setenv bootcmd 'mmc init; fatload mmc 0 80800000 uImage; bootm 80800000'
OMAP3_EVM# setenv bootargs 'mem=76M@0x80000000 mem=128M@0x88000000 console=tty0
console=ttyS0,115200n8 androidboot.console=ttyS0 root=/dev/mmcblk0p2 rw rootfstype=ext3
init=/init rootwait ip=off omap_vout.vid1_static_vrfb_alloc=y
omapdss.def_disp=dvi omapfb.mode=dvi:1280x720MR-16 mpurate=1000'
OMAP3_EVM# saveenv
```


The configuration in the example above sets the display to the DVI port. For more details of display mode see [modedb Documentation](http://www.mjmwired.net/kernel/Documentation/fb/modedb.txt).

## Test ##

To test media playback copy media files to the **third** partition of the SD card and use 'Music player' or 'Gallery' UI applications in rowboat. Supported formats are available on [OMX DSP-accelerated audio/video codecs](http://code.google.com/p/rowboat/wiki/DSPCodecs#Supported_decoding_formats) wiki page.
Check for 'omx-dsp' tag in logcat to see that accelerated codecs are enabled.

If you have any video clip which should be supported but cannot play on rowboat, please try to extract the video elementary stream and play it with the DVSDK decode demo.

The _top_ command shows the ARM load on DM3730@1GHz is about 4~8% when playing H.264@HP video clips in 720P resolution with AAC codec running on ARM side.


# Instruction Difference for Build and Run Froyo+DSP #

## Preparation ##
_java5-jdk_ is required for compiling Froyo. (Since Gingerbread _java6-jdk_ is required.)

Use manifest _rowboat-froyo-dsp.xml_ to download Froyo source from Rowboat.

## Build ##

To build Rowboat Froyo+DSP use the following command:
```
# make TARGET_PRODUCT=[omap3evm | beagleboard | igepv2] [OMAPES=(2.x|3.x|5.x)]
```

Your must specify your board with **TARGET\_PRODUCT** variable. Set **OMAPES** variable to install proper version of SGX drivers (Default is 3.x):
```
OMAPES=2.x, for OMAP3530 ES1 or ES2;
OMAPES=3.x, for OMAP3530 ES3.0;
OMAPES=5.x, for DM37x
```

To get _MLO_ and _u-boot.bin_ for OMAP35x/DM37x EVM, download the [TI Froyo Devkit 2.2 DM37x prebuilt image package](http://software-dl.ti.com/dsps/dsps_public_sw/sdo_tii/TI_Android_DevKit/02_02_00/exports/DM37X.tar.gz). To get the same for Beagleboard-xM, download the [Beagleboard-xM Froyo prebuilt image package](http://software-dl.ti.com/dsps/dsps_public_sw/sdo_tii/TI_Android_DevKit/02_02_00/exports/beagleboard-xm.tar.gz).

## Boot ##
For DM37x EVM and Beagleboard-xM, use the following memory hole setup in bootargs.
```
 'mem=68M@0x80000000 mem=128M@0x88000000'
```

For OMAP35x EVM and Beagleboard, use the following memory hole setup in bootargs.
```
 'mem=104M@0x80000000 mem=128M@0x88000000'
```


# Known Issues #
  * dragging the playback progress bar causes a/v out of sync with some video clips (in Froyo only);


# Some Observations #

  * Sometimes Jave VM complains about SIGSEGV during compiling rowboat. Please re-run the make command if it happens.
  * If there is any failure while compiling dvsdk packages, and you want to redo a clean dvsdk build, please delete **external/ti-dsp/already\_clean** and **external/ti-dsp/ti-dvsdk`_``<`_version_`>`/** and re-run the build command. If **external/ti-dsp/already\_clean** exists, the build script will not install and patch dvsdk packages again. Please refer to the top level Makefile for details.