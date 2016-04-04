# Introduction #

The DM3730 has a powerful C64x+ DSP core embedded in the SoC. This DSP core can be used for various purposes, including multimedia decoding/encoding. This offloads the host ARM processor for other general processing tasks and enables decoding of higher bitrate and/or higher resolution video and audio than would otherwise be achievable with Android running on ARM only.

[Rowboat](http://code.google.com/p/rowboat/) DSP support is based on the TI Linux Digital Video Software Development Kit (http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/latest/index_FDS.html DVSDK) for the DM3730.

This page contains information about how to download, build and enable hardware acceleration for multimedia features of Android on DM37x EVM.

Components Used:
> - Android: 2.3.4<br>
<blockquote>- Linux Kernel: 2.6.37 <br>
- TI DVSDK: DVSDK_4.03 04_03_00_06 <br></blockquote>

<h1>Out of the Box Demo</h1>

This section gives the instructions to quickly prepare an SD Card image and get an experience of Android Gingeread-2.3.4 on DM37x EVM.<br>
<br>
<b>Download the pre-built Image</b>

<pre><code> Using Curl<br>
 $ cd ~<br>
 $ curl http://rowboat.googlecode.com/files/TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x.tar.gz &gt; TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x.tar.gz<br>
 $ tar -zxvf TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x.tar.gz<br>
<br>
 Direct Download<br>
http://rowboat.googlecode.com/files/TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x.tar.gz <br>
$ tar -zxvf TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x.tar.gz<br>
<br>
</code></pre>

<b>Get an SD Card of minimum size 4 GBytes (Class4 minimum) and a USB Card reader</b>

<b>Insert the USB SD Card reader (with SD Card) in your host Linux PC</b>

<b>Prepare the MMC/SD card Image</b>

<pre><code> $ cd ~/TI_Android_Gingerbread_2.3.4_Kernel_2.6.37_dm37x<br>
 $ sudo ./mkmmc-android.sh &lt;Your SD card device e.g:/dev/sdc&gt;<br>
</code></pre>

<b>Setup the board</b>

<pre><code>1. Insert the SD Card into the Board<br>
2. Switch on the board<br>
3. Wait for ~40 sec to get Android up on the UI screen <br>
<br>
</code></pre>


<h1>Getting Source Code</h1>

<h2>Installing Repo</h2>

To install, initialize, and configure repo, follow these steps<br>
<br>
<b>Make sure you have a bin/ directory in your home directory, and that it is included in your path</b>

<pre><code> $ mkdir ~/bin<br>
 $ PATH=~/bin:$PATH<br>
</code></pre>

<b>Download the repo script and ensure it is executable</b>

<pre><code> $ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo &gt; ~/bin/repo<br>
 $ chmod a+x ~/bin/repo<br>
</code></pre>

<h2>Initializing repo Client</h2>

After installing repo, set up your client to access the android source repository<br>
<br>
<b>Create an empty directory to hold your working files</b>

<pre><code> $ mkdir ~/rowboat-android<br>
 $ cd ~/rowboat-android<br>
</code></pre>

<b>Get the Files.</b>

<pre><code> $ repo init -u git://gitorious.org/rowboat/manifest.git -m rowboat-gingerbread-dsp.xml<br>
 $ repo sync<br>
</code></pre>

<b>Download TI DVSDK</b>

Manually download the TI DVSDK package "dvsdk_dm3730-evm_04_03_00_06_setuplinux" to the external/ti-dsp folder from the table in web page <a href='http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/latest/index_FDS.html'>http://software-dl.ti.com/dsps/dsps_public_sw/sdo_sb/targetcontent/dvsdk/DVSDK_4_00/latest/index_FDS.html</a>. Registration might be needed.<br>
<br>
<pre><code> $ cp dvsdk_dm3730-evm_04_03_00_06_setuplinux ~/rowboat-android/external/ti/dsp<br>
</code></pre>

<h1>Download Tools</h1>

Download SD card utility scripts, SignGP and boot script tools & untar them into your home directory.<br>
<br>
<pre><code>cd ~<br>
curl http://rowboat.googlecode.com/files/RowboatTools.tar.gz &gt; RowboatTools.tar.gz<br>
tar -zxvf RowboatTools.tar.gz<br>
</code></pre>

<h1>Compilation Procedure</h1>

<h2>Toolchain Setup</h2>

Setup the tool-chain path to point to arm-eabi- tools in prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin<br>
<br>
<pre><code> $ export PATH=&lt;android source&gt;/rowboat-android/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin:$PATH<br>
</code></pre>

<h2>Build x-loader</h2>

<b>Change directory to x-loader</b>

<pre><code> $ cd x-loader<br>
</code></pre>

<b>Execute the following commands</b>

<pre><code> $ make CROSS_COMPILE=arm-eabi- distclean<br>
 $ make CROSS_COMPILE=arm-eabi- omap3evm_config<br>
 $ make CROSS_COMPILE=arm-eabi-<br>
</code></pre>

This command will build the x-loader Image "x-load.bin"<br>
<br>
To create the MLO file used for booting from a MMC/SD card, sign the x-loader image using the signGP tool.<br>
<br>
<pre><code>  $ ./signGP ./x-load.bin<br>
<br>
 Note: You will need to copy the signGP tool from the Tools/signGP directory to the directory that contains the x-load.bin file<br>
</code></pre>

The signGP tool will create a .ift file, rename the x-load.bin.ift to MLO<br>
<br>
<pre><code> $ mv x-load.bin.ift MLO<br>
</code></pre>

<h2>Build Boot Loader (u-boot)</h2>

<b>Change directory to u-boot</b>

<pre><code> $ cd u-boot<br>
</code></pre>

<b>Execute following commands</b>

<pre><code> $ make CROSS_COMPILE=arm-eabi- distclean<br>
 $ make CROSS_COMPILE=arm-eabi- omap3_evm_config<br>
 $ make CROSS_COMPILE=arm-eabi- <br>
</code></pre>

This command will generate the u-boot Image "u-boot.bin"<br>
<br>
<h2>Build Android, Kernel and SGX</h2>

<pre><code>  $ make TARGET_PRODUCT=omap3evm OMAPES=5.x<br>
</code></pre>

This command will build kernel, android sources and sgx. If you want to build these components separately, follow below section.<br>
<br>
<h2>Create Root Filesystem Tarball</h2>

Prepare the root filesystem as follows:<br>
<br>
<pre><code>  $ cd &lt;android source&gt;/out/target/product/omap3evm<br>
  $ mkdir android_rootfs<br>
  $ cp -r root/* android_rootfs<br>
  $ cp -r system android_rootfs<br>
  $ sudo ../../../../build/tools/mktarball.sh ../../../host/linux-x86/bin/fs_get_stats android_rootfs . rootfs rootfs.tar.bz2<br>
</code></pre>

<h1>Creating an SD Card Image</h1>

This section walks you through the steps necessary to create a bootable SD card with for Android, using the binaries you built in the previous step.<br>
<br>
<h2>Configure Boot Arguments</h2>

<b>DM37x EVM boot arguments:</b>

<pre><code> setenv bootcmd 'mmc init; fatload mmc 0 80800000 uImage; bootm 80800000'<br>
 setenv bootargs 'mem=68M@0x80000000 mem=128M@0x88000000 console=tty0 \ console=ttyO0,115200n8 androidboot.console=ttyO0 root=/dev/mmcblk0p2 rw rootfstype=ext3 init=/init rootwait ip=off mpurate=1000 vram=32M omapfb.vram=0:16M'<br>
<br>
</code></pre>

<b>Generate the boot script (boot.scr) as follows</b>

 Change directory to mk-bootscr folder.<br>
<br>
<pre><code> $ cd ~/RowboatTools/mk-bootscr<br>
</code></pre>

 Edit mkbootscr file as per your boot arguments.<br>
<br>
 Generate boot.scr boot script<br>
<br>
<pre><code> $ ./mkbootscr<br>
</code></pre>

<h2>Flash SD Card</h2>

Copy compiled Images to image folder and create a bootable SD card as follows.<br>
<br>
<pre><code> $ mkdir ~/image_folder<br>
 $ cp &lt;android sorce path&gt;/kernel/arch/arm/boot/uImage ~/image_folder<br>
 $ cp &lt;android souorce path&gt;/u-boot/u-boot.bin ~/image_folder<br>
 $ cp &lt;android source path&gt;/x-loader/MLO ~/image_folder<br>
 $ cp ~/RowboatTools/mk-bootscr/boot.scr ~/image_folder<br>
 $ cp &lt;android source path&gt;/out/target/product/beagleboard/rootfs.tar.bz2 ~/image_folder<br>
 $ cp ~/RowboatTools/mk-mmc/mkmmc-android.sh ~/image_folder<br>
<br>
 $cd ~/image_folder<br>
 $ sudo./mkmmc-android &lt;your SD card device e.g.:/dev/sdc&gt; MLO u-boot.bin uImage boot.scr rootfs.tar.bz2<br>
</code></pre>

<h2>Booting Up the Board</h2>

Insert your SD card into your DM37x EVM, connect serial cable to board and turn on the board. Android should boot.<br>
<br>
Note that the first boot usually takes a little while because it runs some "first-time" initialization scripts. You can connect mouse to board and browse UI.<br>
<br>
- Pankaj Bharadiya (arowboat.org)