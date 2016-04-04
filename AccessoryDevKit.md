<font size='5'>
<b>Beaglebone based - Android Accessory Development Kit</b>
</font>
<br>
<br>
<font size='4'>
<b>User Guide</b>
</font>
<br>
<br>
<font size='3'>
<b>Contents</b>
<br>
</font>
<br>
<h1>Introduction</h1>
The Android platform (from 3.1 onwards) has integrated a specific protocol "Android Open Accessory", which allows external embedded hardware (accessory) to interact with an Android-powered device (Android phone or tablet) in a special accessory mode over USB or Bluetooth interface. Android accessories are specifically designed to attach to Android-powered devices over USB or Bluetooth and adhere to a simple protocol (Android accessory protocol) that allows them to detect Android-powered devices that support accessory mode.<br>
<br>
With majority of smart phones and tablets using Android today, an Android open accessory standard provides a vital link between phones, tablets and other Android powered devices and embedded systems which are pervasive in our daily life.<br>
<br>
Android accessories can be audio docking stations, exercise machines, personal medical testing devices, weather stations, wearable gadgets or any other external hardware device that adds to the functionality of Android.<br>
<br>
As of today, all the available Android accessory kits are based on micro controllers operating at very low MHz (max 150MHz) and limited peripheral set. For the first time, we provide an accessory kit based on TI's AM335x (ARM Cortex A8) application processor. The processor can operate at range of frequencies (100 MHz to 1 GHz), ultra-low power, very broad list of on chip peripherals and more over the system cost is comparable to that of an micro controller. More details about TI's AM335x can be found here www.ti.com/sitara.<br>
<br>
Do we really need an application processor for an accessory ? Yes, the embedded products like home appliance, auto infotainment, personal health care, wearable gadgets, etc. are interacting with phones & tablets - they need an application processor to meet the required processing & peripheral set.<br>
<br>
With the help of rowboat team, we were able to port Android accessory protocol for TI's AM335x based beaglebone platform. Beaglebone + capes based Android accessory kit gives developers the freedom to innovate and develop all new range of accessories for Android devices. It provides an easy to use software development kit, user/developer guide and pre-built examples.<br>
<br>
<h2>Fundamentals of Beaglebone as Android Accessory</h2>

Beaglebone runs the Android accessory protocol (generic) and accessory application specific software. We use the TI's Starterware for Beaglebone as the base software and have developed & integrated the android accessory protocol and a sample application. We have also developed a dummy app (apk) for Android that runs on phone or tablet that interacts with Beaglebone application to control the peripherals on Beaglebone.<br>
<br>
Functionally, Beaglebone is connected to Android powered (phone / tablet) device over USB, the Beaglebone operates USB in host mode (powers the bus and enumerates devices) and the Android-powered device acts as the USB device. Beaglebone runs the Android USB accessory protocol with application software, detects & enumerates the Android-powered device (phone/tablet) and starts the sample application (beaglebone side) that waits for commands from Android device (running sample accessory apk) to control/monitor the Beaglebone peripherals.<br>
<br>
<br>
<img src='http://rowboat.googlecode.com/files/BB_Android_Accessory.png' />
<br>
Why use TI's StarterWare software package as a base software v/s Linux or u-boot ?<br>
<br>
<blockquote>- For Linux, we have to develop a all new USB class for Android accessory and a custom application that talks to peripheral Linux drivers. The implementation is not complex, we have plans to develop this in future.</blockquote>

<blockquote>- u-boot has limited support for AM335x peripherals and we found USB Host protocol lack few features to implement the accessory protocol.</blockquote>

TI's starterware is free to use and distribute, comes with pre-integrated libraries with APIs to access every peripheral on AM335x processor, very easy to use with lots of examples, direct support from TI. For more details please refer to <a href='http://processors.wiki.ti.com/index.php/StarterWare_Getting_Started_02.00.XX.XX'>StarterWare getting started guide</a> and <a href='http://processors.wiki.ti.com/index.php/StarterWare_02.00.00.07_User_Guide'>user guide</a><br>

<h2>Android Accessory over Bluetooth (TBD)</h2>
The current implementation is based on USB, this will be extended to work over Bluetooth. We are waiting for the beaglebone Bluetooth cape availability.<br>
<br>
<h2>Android Accessory over Google Cloud messaging</h2>
Beaglebone can be communicate to Android phone using Google Cloud messaging service, there is an interesting implementation from our community member. Refer to this project for more details.<br>
<br>
<a href='http://www.dwindlingthumbs.com/hardware/beaglebone-based-security-system/'>http://www.dwindlingthumbs.com/hardware/beaglebone-based-security-system/</a>

<h1>Out of Box Demo</h1>

This section gives the instructions to quickly prepare an SD Card with pre-built binaries of beaglebone accessory software  and to experience Beaglebone as Android accessory over USB.<br>
<br>
<h2>Getting Pre-built Images</h2>

To successfully experience beaglebone as an Android accessory we need<br>
<ul><li>Beaglebone Starterware binary (with integrated Android accessory protocol and Accessory application) - <a href='http://rowboat.googlecode.com/files/BeagleBoneAcc.tar.gz'>BeagleBoneAcc.tar.gz</a>
</li><li>A sample Android application (.apk) to be installed on any Android phone or Tablet. - <a href='http://rowboat.googlecode.com/files/TI-ADKDemo.tar.gz'>TI-ADKDemo.tar.gz</a></li></ul>

<h2>Prepartion of SD card</h2>

Connect a Micro SD card via a USB card reader on an Ubuntu Machine<br>From a command terminal, type the below commands,<br>
<br>
<pre><code>   #&gt; tar -xzvf BeagleBoneAcc.tar.gz<br>
   #&gt; cd BeagleBoneAcc<br>
   #&gt; sudo ./mkmmc-acc.sh /dev/sd&lt;device&gt;<br>
</code></pre>
The above step prepares the bootable SD Card with Starterware image, which can be used to boot beaglebone as an Android accessory.<br>
<br>
<h2>Install Accessory Application</h2>

Follow the below steps to install the example Android application on Android phone or tablet (version 4.0.3 or above)<br>
<br>
<ul><li>The apk is located in TI-ADKDemo/bin/TI-ADKDemo.apk<br>
</li><li>Install this application on Android4.0.3 powered device.<br>
</li><li>The phone can be connected to a Windows or Linux PC and the application can be transferred/installed through ADB over USB. This step should be known to developer as it's a standard method to download/install any Android custom apk on phones/tablets.</li></ul>

<h2>Experience Beaglebone as Android accessory</h2>

After preparing the SD card for Beaglebone and installing apk on phone, please follow the steps below:<br>
<br>
<ul><li>Insert the SD card into Beaglebone<br>
</li><li>Connect the power cable and power ON the board<br>
</li><li>Let the phone/tablet be in on state with accessory app running<br>
</li><li>Connect Beaglebone USB host port to mini/Micro-B USB port of the Android device (phone/tablet).<br>
</li><li>Control the LEDs on Beaglebone through the buttons displayed on the Android accessory UI on phone.<br>
</li><li>The phone displays a timer that is synced from RTC running on beaglebone.</li></ul>

This app is just an example to prove beaglebone communication with phone/tablet using Android accessory protocol. Both, the apk on phone and the application on beaglebone can be modified for any type of accessory. The Android accessory protocol need not be changed.<br>
<br>
Watch this video<br>
<br>
<a href='http://www.youtube.com/watch?feature=player_embedded&v=ZlBZJ_vKEFQ' target='_blank'><img src='http://img.youtube.com/vi/ZlBZJ_vKEFQ/0.jpg' width='425' height=344 /></a><br>
<br>
<h1>Building Sources</h1>

The procedure to download the sources, installing the toolchain and compiling the sources to generate the final images (like pre-built images) is described below<br>
<br>
<h2>Getting the Toolchain</h2>

<ul><li>Download CodeSourcery tool chain for ARM for Linux <a href='http://www.codesourcery.com/sgpp/lite/arm/portal/package4465/public/arm-none-eabi/arm-2009q1-161-arm-none-eabi.bin'>LINK</a></li></ul>

<ul><li>Install toolchain on Linux Host machine (ubuntu 10.04 or above)<br>
<pre><code>   #&gt; chmod 777 arm-2009q1-161-arm-none-eabi.bin<br>
   #&gt; ./arm-2009q1-161-arm-none-eabi.bin<br>
</code></pre></li></ul>

<ul><li>Set PATH environment variable contain the path of the compiler/tool chain.</li></ul>

<pre><code>   Example:<br>
   #&gt; export PATH=$PATH:/opt/tools/CodeSourcery/Sourcery_G++_Lite/bin<br>
</code></pre>
<ul><li>Point LIB_PATH shell environment variable to the Code Sourcery installation</li></ul>

<pre><code>   Example:<br>
   LIB_PATH=/opt/tools/CodeSourcery/Sourcery_G++_Lite<br>
</code></pre>

<blockquote>Note: /opt/tools/ is the path selected while installing the toolchain. If you are installing toolchain path at some other location, please set PATH and LIB_PATH variable appropriately.</blockquote>

<h2>Getting Source code</h2>

As mentioned above, we use TI's StarterWare software package to develop Beaglbone Android accessory.<br>
<br>
<ul><li>Download StartWare Linux Installer package from <a href='http://software-dl.ti.com/dsps/dsps_public_sw/am_bu/starterware/02_00_00_07/index_FDS.html'>HERE</a>
</li><li>Install StarterWare package<br>
<pre><code>   #&gt; &lt;path to package dir&gt;/AM335X_StarterWare_02_00_00_07_Setup.bin<br>
</code></pre>
</li><li>Download the Beaglebone Accessory patch to StarterWare <a href='http://rowboat.googlecode.com/files/adk_patch.tar.gz'>from HERE</a>
</li><li>Apply the patch<br>
<pre><code>    #&gt; cd &lt;StarterWare 02.00.00.07&gt;<br>
    #&gt; patch -p1&lt; &lt;patch to patch directory&gt;/adk_patch/0001-Add-beaglebone-Accessory-support.patch<br>
</code></pre></li></ul>

<h2>Compile the Source Code</h2>

<h3>Building The Bootloader</h3>

Use below commands to build the StarterWare bootloader, this will generate boot_ti.bin image at <Path to StarterWare code>/binary/armv7a/gcc/am335x/beaglebone/bootloader/Release<br>
<br>
<pre><code>   #&gt; cd &lt;Path to StarterWare Code&gt;/build/armv7a/gcc/am335x/beaglebone/bootloader<br>
   #&gt; make<br>
<br>
NOTE: Rename "boot_ti.bin" to MLO.<br>
<br>
   #&gt; mv &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/bootloader/Release/boot_ti.bin &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/bootloader/Release/MLO<br>
</code></pre>

<h2>Building StarterWare Android Accessory Application</h2>

Use below command to build the sample Beaglebone accessory application for StarterWare. This will generate an application usb_acc_ti.bin binary at <Path to StarterWare code>/binary/armv7a/gcc/am335x/beaglebone/usb_acc/Release/usb_acc_ti.bin<br>
<br>
<pre><code>   #&gt; cd &lt;Path to StarterWare Code&gt;/build/armv7a/gcc/am335x/beaglebone/usb_acc<br>
   #&gt; make<br>
<br>
NOTE: Rename Application "usb_acc_ti.bin" to "app".<br>
<br>
   #&gt; mv &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/usb_acc/Release/usb_acc_ti.bin &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/usb_acc/Release/app<br>
</code></pre>

<h2>Prepare the bootable SD/MMC card</h2>

Copy compiled images and mk-mmc-acc.sh script to image folder and populate SD/MMC card as follows.<br>
<br>
<pre><code>   #&gt; mkdir ~/image<br>
   #&gt; cd ~/image<br>
   #&gt; cp &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/bootloader/Release/MLO .<br>
   #&gt; cp &lt;Path to StarterWare code&gt;/binary/armv7a/gcc/am335x/beaglebone/usb_acc/Release/app .<br>
   #&gt; cp &lt;path to pre-built image&gt;/BeagleBoneAcc/mkmmc-acc.sh .<br>
   #&gt; sudo ./mkmmc-acc.sh /dev/sd&lt;device&gt;<br>
</code></pre>

Above steps will create a bootable SD/MMC card which should be similar to pre-built images and can be used to bootup beaglebone.<br>
<br>
Follow the steps mentioned above in the Out of the box demo section to boot the board and to use the beaglebone as an Android accessory.<br>
<br>
<h1>Customizing software to develop new accessory</h1>

The software stack contains the<br>
<ul><li>TI's StarterWare Package (<i>Downloaded from TI's site</i>)<br>
</li><li>USB class driver for Android accessory (<i>Newly Implemented</i>)<br>
<ul><li>Implemented using the StarterWare's USB stack which adheres to the Android open accessory standard. The detailed design of StarterWare USB stack is provided <a href='http://processors.wiki.ti.com/index.php/StarterWare_USB#Core_Design'>HERE</a>
</li><li>Sample Accessory application (<i>Newly Implemented</i>)<br>
</li><li>Uses StarterWare libraries to control the hardware and interact with Android Phone/Tablet</li></ul></li></ul>

The block diagram of Beaglebone USB accessory software is as shown below.<br>
<br>
<br>
<img src='http://rowboat.googlecode.com/files/BB_accessory_stack.png' />
<br>
<br>

<blockquote>NOTE: To develop a new accessory, one has to modify only the StarterWare application and the phone side apk. The USB class driver remains unchanged.</blockquote>

<h2>Host Controller driver</h2>

The USB host controller driver handles the discovery and enumeration of any USB device. The USB host controller driver only performs enumeration and relies on the host accessory class driver to perform any other communications with Android device. Most of the code used to enumerate devices is run in interrupt context and is contained in the enumeration handler. In order to complete the enumeration process, the host controller driver also requires that the application periodically call the USBHCDMain() function.<br>
<br>
The application has to register a USB accessory class with host controller driver using USBHCDRegisterDrivers() call so that enumeration events can be passed to it.<br>
<br>
Please refer to <a href='http://processors.wiki.ti.com/index.php/StarterWare_USB'>StarterWare USB</a> documentation for more details about StarterWare USB.<br>
<br>
<h2>USB Accessory Class Driver</h2>

The USB Accessory class driver provides an interface to detect and set up communication with an Android accessory powered device (phone/tablet). In general, an accessory class driver carries following steps.<br>
<br>
<ol><li>Determine the device's accessory mode support<br>
</li><li>Attempt to start the device in accessory mode if needed<br>
</li><li>Establish communication with the device if it supports the Android accessory protocol<br>
</li><li>Provides accessory read and write APIs to communicate with Android accessory powered device.</li></ol>

Application has to call USBACCOpen() to register a callback. The Accessory class driver will call this registered callback to notify whether<br>
<br>
<ol><li>USB Accessory device is connected<br>
</li><li>Unknown device is connected<br>
</li><li>Device is disconnected.</li></ol>

Once Device connected (USB_EVENT_CONNECTED) event is received, application can read and write to accessory device using AccessoryRead() and AccessoryWrite() calls<br>
<br>
<h2>Accessory Application</h2>

Below block diagram demonstrate the Beaglebone accessory application code flow.<br>
<br> <br>
<img src='http://rowboat.googlecode.com/files/BB_Accessory_Flow.png' />

The application must register a USB accessory class driver by calling USBHCDRegisterDrivers() and call USBACCOpen to register a callback. The callback will get invoked by accessory class driver to indicate whether<br>
<br>
<ol><li>Android accessory mode device is connected (USB_EVENT_CONNECTED)<br>
</li><li>Unknown device is connected (UNKNOWN_DEVICE_EVENT)<br>
</li><li>Device is disconnected (USB_EVENT_DISCONNECTED).</li></ol>

Once the device connected event is received, accessory can communicate with the application running on Android powered device using custom communication protocol.<br>
<br>
The example application executes the following sequence:<br>
<br>
<ol><li>Configure and enable the interrupts<br>
</li><li>Enable the USB clocking<br>
</li><li>Register the accessory host class driver<br>
</li><li>Open an instance of the accessory class driver<br>
</li><li>Initialize the power configuration<br>
</li><li>Initialize the host controller<br>
</li><li>Initializes user LED GPIO<br>
</li><li>Enables RTC<br>
</li><li>Periodically calls USBHCDMain() function to complete USB re-enumeration process<br>
</li><li>Sends RTC time data to Android Accessory powered device, receives LED control information and configure LEDs.</li></ol>

<h3>To customize accessory application on Beaglebone</h3>

<h4>Directory structure</h4>

<ul><li>Place the application code in "<path to StarterWare source>/examples/beaglebone/<your app folder name>"</li></ul>

<ul><li>Place the makefile and linker script in "<path to StarterWare source>/build/armv7a/gcc/am335x/beaglebone/<your app folder name>" directory.</li></ul>

<ul><li>Follow below command to build the customized application<br>
<pre><code>#&gt; cd &lt;path to StarterWare source&gt;/build/armv7a/gcc/am335x/beaglebone/&lt;your app folder name&gt;<br>
#&gt; make<br>
</code></pre></li></ul>

<blockquote>Note: Refer of makefile and linker script present in "<path to StarterWare Source>/build/armv7a/gcc/am335x/beaglebone/usb_acc" directory.<br>
</blockquote><ul><li>Generated binaries will be placed in folder "<path to StarterWare source>/binary/armv7a/gcc/am335x/beaglebone/<your app folder name></li></ul>

<h4>Example accessory application snippet</h4>

Following code snippet gives a rough idea on how to develop a new accessory application using StarterWare package.<br>
<br>
<ul><li>Declare structure to use host accessory class driver</li></ul>

<font size='2'><pre><code><br>
static tUSBHostClassDriver const * const g_ppHostClassDrivers[] =<br>
{<br>
&amp;g_USBACCClassDriver,<br>
&amp;g_sUSBEventDriver<br>
};<br>
</code></pre> </font>

<ul><li>Declare global variables to store accessory instance value and device connection state</li></ul>

<font size='2'><pre><code><br>
static unsigned int g_ulACCInstance;<br>
static unsigned int eUSBState;<br>
</code></pre> </font>

<ul><li>Implement a callback function so that application can be informed when an Android accessory powered device is connected and disconnected.</li></ul>

<font size='2'><pre><code><br>
<br>
unsigned int ACCCallback(unsigned int ulEvent)<br>
{<br>
switch(ulEvent)<br>
{<br>
case USB_EVENT_CONNECTED:<br>
{<br>
<br>
eUSBState = STATE_ACC_INIT;<br>
break;<br>
}<br>
<br>
case USB_EVENT_DISCONNECTED:<br>
{<br>
eUSBState = STATE_NO_DEVICE;<br>
break;<br>
}<br>
<br>
case UNKNOWN_DEVICE_EVENT:<br>
{<br>
eUSBState = STATE_UNKNOWN_DEVICE;<br>
break;<br>
}<br>
}<br>
}<br>
</code></pre> </font>

<ul><li>Implement a main loop that runs the application</li></ul>

<font size='2'><pre><code><br>
int main(void)<br>
{<br>
MMUConfigAndEnable();<br>
<br>
/*configure arm interrupt controller to generate usb interrupt */<br>
<br>
/*Register the host class driver*/<br>
USBHCDRegisterDrivers(USB_INSTANCE, g_ppHostClassDrivers, 1);<br>
<br>
/* Open an instance of the accessory driver and register a callback*/<br>
g_ulACCInstance = USBACCOpen(USB_INSTANCE, ACCCallback);<br>
<br>
USBHCDPowerConfigInit(USB_INSTANCE, USBHCD_VBUS_AUTO_HIGH);<br>
<br>
/* Initialize the host controller stack */<br>
USBHCDInit(USB_INSTANCE, g_pHCDPool, HCD_MEMORY_SIZE);<br>
USBHCDTimeOutHook(USB_INSTANCE, &amp;USBHTimeOut);<br>
USBHTimeOut-&gt;Value.slNonEP0= 1;<br>
<br>
/*Do other necessary initialization specific to your application*/<br>
<br>
/* Call the main loop for the Host controller driver */<br>
USBHCDMain(USB_INSTANCE, g_ulACCInstance);<br>
<br>
/* Implement main loop for the application */<br>
while(1)<br>
{<br>
switch(eUSBState)<br>
{<br>
/* This state is entered when android accessory powered device is first detected */<br>
case STATE_ACC_INIT:<br>
{<br>
/* Do necessary initialization */<br>
break;<br>
}<br>
case STATE_ACC_CONNECTED:<br>
{<br>
/* Do accessory read and write operations.<br>
* Implement your custom accessory communication protocol to<br>
* communicate with Android application<br>
* running on Android accessory powered device.<br>
*/<br>
break;<br>
}<br>
case STATE_NO_DEVICE:<br>
{<br>
break;<br>
}<br>
default:<br>
{<br>
break;<br>
}<br>
}<br>
<br>
/* Periodically call the main loop for the Host controller driver */<br>
USBHCDMain(USB_INSTANCE, g_ulACCInstance);<br>
}<br>
</code></pre> </font>

<h2>To customize accessory application on Android Device (Phone/Tablet)</h2>

Please refer to <a href='http://developer.android.com/guide/topics/connectivity/usb/accessory.html'>Google USB accessory API guide</a> and TI Accessory Demo Android application code to write your own accessory application that can interact with the Beaglebone.<br>
<br>
<h1>APPENDIX</h1>

<h2>Configuring Beaglebone to get serial console</h2>

Serial console is provided via MiniB USB connection between the BeagleBone and the Host PC. The below instructions guides the user to download the necessary drivers and configure the host to get the console over USB<br>

<h3>On Windows Host PC</h3>

NOTE: The USB to console driver <a href='http://beagleboard.org/static/beaglebone/a3/Drivers/Windows/BONE_DRV.exe'>BONE_DRV.exe</a> for 32-bit Windows PC or <a href='http://beagleboard.org/static/beaglebone/a3/Drivers/Windows/BONE_D64.exe'>BONE_D64.exe</a> for 64-bit Windows PC can be downloaded and installed on the host PC, unfortunately we haven't tried this directly.<br>
<br>
The FTDI driver for Windows XP can be downloded from <a href='http://www.ftdichip.com/Drivers/CDM/CDM20814_WHQL_Certified.zip'>HERE</a>

Follow the below steps to install the FTDI driver on Windows XP machine<br>
<br>
<ul><li>Extract the contents and edit the ftdibus.inf file<br>
</li><li>Replace the section between<br>
<pre><code>[FtdiHw] <br>
<br>
...<br>
... <br>
<br>
[FtdiHw.NTamd64] <br>
</code></pre></li></ul>

with the following content<br>
<br>
<pre><code>USB\VID_0403&amp;amp;PID_A6D0.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_02.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_02<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_03.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_03<br>
USB\VID_0403&amp;amp;PID_A6D0.DeviceDesc%=FtdiBus.NT,USB\VID_0403&amp;amp;PID_A6D0<br>
</code></pre>

<ul><li>Replace the section between</li></ul>

<pre><code>DriversDisk="FTDI USB Drivers Disk" <br>
<br>
and <br>
<br>
SvcDesc="USB Serial Converter Driver" <br>
</code></pre>

with the following content<br>
<br>
<pre><code>USB\VID_0403&amp;amp;PID_A6D0.DeviceDesc="USB Serial Converter"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00.DeviceDesc="USB Serial Converter A"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01.DeviceDesc="USB Serial Converter B"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_00.DeviceDesc="USB Serial Converter A"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_01.DeviceDesc="USB Serial Converter B"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_02.DeviceDesc="USB Serial Converter C"<br>
USB\VID_0403&amp;amp;PID_A6D0&amp;amp;MI_03.DeviceDesc="USB Serial Converter D"<br>
USB\VID_0403&amp;amp;PID_A6D0DeviceDesc="USB Serial Converter"<br>
</code></pre>

<ul><li>Follow the steps listed below to setup the console<br>
<ul><li>Boot the board<br>
</li><li>Connect Mini B USB cable between board and Windows PC.<br>
</li><li>If it's proceeding as planned, Windows should tell you it found a new hardware and asks you to install the driver. Install driver that was downloaded as described in the above step<br>
</li><li>Answer "No, not this time" to the question about running Windows Update to search for software.<br>
</li><li>Choose "Install the hardware that I manually select from a list (Advanced)" this is the 2nd option, then click "Next"<br>
</li><li>Select "Show All Devices", then click "Next"<br>
</li><li>You are going to see a grayed-out text box with "(Retrieving a list of all devices)", click the "Have Disk..." button<br>
</li><li>Browse" to your driver folder (c:\...\<i>driver). It will be looking of a .inf file so select "ftdibus.inf" and click "Open" then "OK".<br>
</li><li>Select "USB Serial Port" then click the "Next" button.<br>
</li><li>A warning will appear, answer "Yes" but read the warning anyway.<br>
</li><li>Click the "Close" when the wizard is completed.<br>
</li><li>Disconnect and reconnect Mini B USB cable from Board(probably reboot it as well).<br>
</li><li>Serial COM port will be listed on the Terminal Utility menu<br>
</li><li>Adjust the baudrate to 115200 to connect to the BeagleBone serial.</li></ul></li></ul></i>

<h3>On Linux Host PC</h3>

To configure serial console output on ubuntu kindly follow the steps listed below:<br>
<pre><code>#&gt; sudo modprobe ftdi_sio vendor=0x0403 product=0xa6d0<br>
#&gt; minicom -D /dev/`dmesg | grep FTDI | grep "now attached to" | tail -n 1 | awk '{ print $NF }'` <br>
</code></pre>

<blockquote>Note:<br>
</blockquote><ul><li>Make sure minicom's serial port setup is for 115200n8 with hardware flow control. The minicom configuration menu can be invoked by the command 'minicom -s' and selecting serial port setup - Serial Device (option A).<br>
</li><li>Run minicom with sudo, in case sufficient permission is not available for the USB serial node</li></ul>

<h3>Verifying Serial Connection<br></h3>

Once serial console setup is complete, confirm the configuration by following the steps given below<br>
<br>
<ul><li>Push the small black reset button beside ethernet port on Beaglebone<br>
</li><li>If serial connection was configured properly, the sequence 'CCCC' should be observed on the serial console terminal.</li></ul>

<h3>Powering ON Beaglebone Accessory</h3>

Inster the populated Micro SD card into the slot on the BeagleBone. Press the reset button, the following text should appear on the console.<br>
<br>

<pre><code> StarterWareAM335x Boot Loader<br>
 Copying application image from MMC/SD card to RAM<br>
 Jumping to StarterWare Application...<br>
  .<br>
  .<br>
  .<br>
</code></pre>

The accessory application holds the control <br>

<h1>Support</h1>
For any further inputs and to participate in this project please feel free to contact us at rowboat@googlegroups.com