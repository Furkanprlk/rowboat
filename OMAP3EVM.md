# Introduction #

This page collects hardware-related notes about Android on OMAP3EVM. It also contains information regarding features, limitations and other board-specific topics relevant to Android

You can get information regarding OMAP3EVM here:
  * Getting started: http://www.mistralsolutions.com/products/omap_3evm.php
  * Detailed information: http://www.mistralsolutions.com/assets/downloads/3530.php


### Board Revisions Tested ###
  * **Generation I.**CPU board: rev. **B**; Main board: rev. **D**; MDC board: rev. **B**
  * **Generation II.**CPU board: rev. **C**; Main board: rev. **G**; no MDC required for this generation of Main board

### Bootloader information ###
We recommend to use uboot from TI PSP SDK release 02.01.02.09.

You can get pre-built stuff here: http://rowboat.googlecode.com/files/u-boot.evm.bin

### Booting Android sample ###
TBD

### Board features/devices supported: ###
  * Serial
  * NAND flash
  * MMC
  * Ethernet
  * keypad
  * LCD
  * Touchscreen
  * Audio
  * USB OTG port (Host mode)

### On-board keys mapping in Android ###
The onboard key are mapped in the following way (from the upper left corner to the lower right by the rows):

|Menu       |Back     |Up     |Home      |Explorer|
|:----------|:--------|:------|:---------|:-------|
|Volume Up  |Left     |Center |Right     |Search  |
|Volume Down|Soft Left|Down   |Soft Right|Power   |

### Using Multimedia Daughter Card (MDC) in Android (Relevant for Generation I EVMs) ###
TBD

### Using DVI output instead of on-board LCD ###
If you need external display you need to enable additional kernel parameters:
  * omapdss.def\_disp=dvi - this specifies to use DVI output instead on-board screen
  * omapfb.mode=dvi:720x480MR-16@60 - this specifies the resolution for external display
or (in older versions of u-Boot)
  * omap-dss.def\_disp=dvi - this specifies to use DVI output instead on-board screen
  * omapfb.video\_mode=720x480MR-16@60 - this specifies the resolution for external display
Here 720x480MR-16@60 is example value which means:
  * resolution - 720x480
  * M - always present
  * R - reduced blinking
  * 16 - 16 bits = 65,536 colours
  * @60 - 60 Hz
For more detailed info see [modedb Documentation](http://http://www.mjmwired.net/kernel/Documentation/fb/modedb.txt)

### Using TV output (S-Video) instead of on-board LCD ###

It is similar to usage of DVI output. If you want to use S-Video - make sure the cable is connected to EVM S-Video Output connector (not to confuse with Input!!!).

Pass following parameters in the command line:
  * omapdss.def\_disp="tv"
  * omapfb.mode="tv:ntsc"
or (in older versions of u-Boot)
  * omap-dss.def\_disp="tv"
  * omapfb.video\_mode="tv:ntsc" or "tv:pal"

### Known issues, limitations ###
RTC does not work on Rev D board, you can find more details here: http://e2e.ti.com/forums/t/9023.aspx. Latest EVM does not have this limitation.