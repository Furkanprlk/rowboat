Most questions are easily found with Google... but here's a short list of questions already encountered. If you're still stuck, consider sending an email to rowboat@googlegroups.com or trying the #rowboat IRC chat.

## Android Apps ##

### How can I change the wallpaper? ###
  * Hold the MENU button (key S4 on the OMAP3EVM), select wallpaper, and browse to the picture.
  * To load a xxx.png from your host, you can use "`adb push `<path to file>` /sdcard`"

### How can I prevent the LCD from sleeping? ###
  * After booting Android, select "Settings"->"Sound & Display"->"Screen Timeout"
  * Note: If the EVM sleeps, then the adb connection may stop functioning, thus for development, it is recommended to change the Screen Timeout to "Never Timeout"

### What if the Gallery App doesn't see my local media files? ###
  * In adb shell: "`am broadcast -a android.intent.action.MEDIA_MOUNTED --ez read-only false -d file:///sdcard`"

### How do I find the target's IP address? ###
  * On the target terminal, "`netcfg`" - if you don't see the IP address, try "`netcfg eth0 dhcp`"
  * If you can't bring up the Ethernet, double check that the ethaddr was set up u-boot (i.e. "`set ethaddr 00:50:c2:xx:xx:xx`")

### How do I connect via adb? ###
  * Assuming you have installed the Android SDK and <android-sdk-x.xx>/tools is on your PATH:
    * `$ ADBHOST=xxx.xxx.xxx.xxx adb kill-server` (note ADBHOST is the IP address of the target)
    * `$ ADBHOST=xxx.xxx.xxx.xxx adb shell`

### How do I install an app? ###
  * `$ ADBHOST=xxx.xxx.xxx.xxx adb -s device-5554 install <my/path/to>/CoolApp.apk`
  * You can see the list of all connected devices with "`adb devices`"

## Development Questions ##
### Why does the first boot take so much time? ###
  * It's by design - During first start Android installs all default prebuilt applications and configures settings to default state.

### How do I get around firewall problems? ###
  * See https://omapzoom.org/gf/project/omapandroid/wiki/?pagename=Git+Firewall

### How do I install BusyBox? ###
  * https://omapzoom.org/gf/project/omapandroid/wiki/?pagename=Installing+Busybox+command+line+tools

### What about bash? ###
  * http://www.kbrandt.com/2009/06/how-to-cross-compile-the-bash-shell-for-android-15.html

### How do you mount an NFS after the board has booted from SD? ###
  * `$ mount -t nfs -o nolock xxx.xxx.xxx.xxx:/path/to/nfs`

### Where did the project name 'rowboat' come from? ###
  * Someone simply added a 'w' and an 'a' to 'robot' and the name stuck.

### Please post your questions to rowboat@googlegroups.com instead of posting below ###