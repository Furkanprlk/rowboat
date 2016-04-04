# Introduction #

This page collects hardware-related notes about Android on BeagleBoard. It also contains information regarding features, limitations and other board-specific topics relevant to Android

We strongly recommend to get familiar with BeagleBoard resources at **[beagleboard.org](http://beagleboard.org)**

### Board Revisions Tested ###
We are verifying regular builds on **C3** revision. It also expected work on **B7** and **C2**

### Bootloader information ###
We are using following bootloader version: X-Loader 1.4.2. U-Boot 2009.08.

You can get pre-built stuff here: http://rowboat.googlecode.com/files/u-boot.beagle.bin or download and build uboot manually from http://gitorious.org/u-boot-omap3

### Booting Android sample ###
TBD

### Board features/devices supported: ###
  * Serial
  * USB Host port
  * USB OTG port (Host mode)
  * MMC
  * GPIO USER key
  * Audio
  * HDMI out

### On-board keys mapping in Android ###
On board key "USER" is mapped to Home.

### Using TV output (S-Video) instead of on-board LCD ###

It is similar to usage of DVI output.

Pass following parameters in the command line:

  * omap-dss.def\_disp="tv"
  * omapfb.video\_mode="tv:ntsc" or "tv:pal"


### Known issues, limitations ###
TBD