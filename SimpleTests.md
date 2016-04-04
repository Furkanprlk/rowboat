So let's see what "Simple Tests" are available and how to run them

# Dalvik Unit Tests #
You can find it in `<source_top>/dalvik/tests`. Use `./run-all-tests` to run all tests, or `./run-test <test>` to run a single specified test.
Run `./run-test` with no arguments to see command flags;
Note: Some features described in help for these tools aren't work.

## Useful tests ##
We check all every test's source and thought what useful are:
  * 001 - 004
  * 010 - 012
  * 014 - 016
  * 018 - 019
  * 021 - 037
  * 039
  * 041
  * 043 - 044
  * 051
  * 053 - 071
TDB:Add descriptions and comments

## Results ##
TBD:Add results from OMAP3EVM, Beagleboard and emulator

# Android Unit Tests #
## Core Unit Tests ##
TBD: Add description & results

## Libcore Unit Tests ##
TBD: Add description & results

# Android API Demo Tests #
This groups of the test show us which functionality work find and which not. As reference use Google Android 1.6\_r1 API Demos.
API Demos -> App -> All
API Demos -> Graphics -> All
API Demos -> Views -> All
TBD: Add results

# Applications #
## WebBrowser ##
To test Web Browser you will need direct Internet connection.
If it’s possible use network with DHCP server - connect board to it, then
```
#netcfg eth0 dhcp
#setprop net.eth0.dns1 <your_dns_server_ip>
#ping www.google.com
```
TBD: In new version we have `ethmonitor` utility [[br](br.md)]
After you established Internet connection do following:
  * Basic functionality
Launch WebBrowser form main menu or from desktop, you see Web Browser window with Google’s page if everything is fine. Type on USB Keyboard “developer.android.com” and hit “Enter” or “Ctrl”. The pointed site opens, compare this with the same site but opened on your host. Zoom in and zoom out, try “1x” and “X” buttons. If everything looks good (note what WebBrowser doesn’t work with frames) and application doesn’t crash - test passed.
  * Advanced functionality
Go to “flickr.com” and try to view some images.
'<br>'TBD:Need to rewrite this'<br>'<br>
If every looks fine - test passed.<br>
<ul><li>WebBrowser and Proxy<br>
To test proxy feature you’ll need proxy server. You can search for free proxy the Internet or use this : 75.66.32.113:80<br>
Login to target board via <code>adb</code> and set system provider’s property <code>http_proxy</code> to yours proxy:<br>
<pre><code>#sqlite3 /data/data/com.android.providers.settings/databases/settings.db "INSERT INTO secure VALUES\(99,'http_proxy','75.66.32.113:80');"<br>
</code></pre>
Check to see if your proxy was added correctly by issuing the following:<br>
<pre><code>#sqlite3 /data/data/com.android.providers.settings/databases/settings.db "SELECT * FROM secure"<br>
...<br>
99|http_proxy|75.66.32.113:80<br>
</code></pre>
Now launch WebBrowser and try to open <a href='http://whatismyipaddress.com/'>http://whatismyipaddress.com/</a> to see your 	current IP address. If IP address is proxy’s address - test passed.<br>
</li><li>TBD:3rd party applications<br>
</li><li>TBD:Uninstall (need to rewrite)<br>
There are two ways for it:<br>
<ul><li>Standard way. Main menu -> Menu -> Settings -> Applications -> Manage applications -> Find “TBD” -> Tap on it -> Uninstall -> OK -> OK -> Check the list. Go to Main menu -> Search for just uninstalled app, if it’s not here - test passed.<br>
</li><li>Service way. Use ADB on your host:</li></ul></li></ul>

<h1>Hardware Specific Tests</h1>
<h2>OMAP3EVM Keypad</h2>
<ul><li>key 25    MENU<br>
</li><li>key 24    BACK<br>
</li><li>key 49    DPAD_UP<br>
</li><li>key 37    HOME<br>
</li><li>key 36    EXPLORER<br>
</li><li>key 23    VOLUME_UP<br>
</li><li>key 28    DPAD_LEFT<br>
</li><li>key 33    DPAD_CENTER<br>
</li><li>key 18    DPAD_RIGHT<br>
</li><li>key 103   SEARCH<br>
</li><li>key 108   VOLUME_DOWN<br>
</li><li>key 48    SOFT_LEFT<br>
</li><li>key 30    DPAD_DOWN<br>
</li><li>key 106   SOFT_RIGHT<br>
</li><li>key 105   POWER</li></ul>

Tap every button on EVM’s keypad and compare the response with mapping. If everything works fine - test passed.<br>
<br>
Here is the keypad layout, with the board oriented in portrait mode with<br>
the keys at the bottom:<br>
<table><thead><th>Menu       </th><th>Back     </th><th>Up     </th><th>Home      </th><th>Explorer</th></thead><tbody>
<tr><td>Volume Up  </td><td>Left     </td><td>Center </td><td>Right     </td><td>Search  </td></tr>
<tr><td>Volume Down</td><td>Soft Left</td><td>Down   </td><td>Soft Right</td><td>Power   </td></tr></tbody></table>

<h3>Provisioning</h3>
In order for some of these keys to work, you may need to set the machine to<br>
be "provisioned".  For example, unless the device is provisioned, the<br>
keyguard code silently discards the HOME key.<br>
<br>
However, by default open builds of the Android have no provisioning<br>
mechanism.  See this thread for info:<br>
<a href='http://groups.google.com/group/android-porting/browse_thread/thread/61baf6058966fe31'>http://groups.google.com/group/android-porting/browse_thread/thread/61baf6058966fe31</a>

To make the code believe it is provisioned, you need to add a setting<br>
to the 'secure' system settings.<br>
(Note that in the above thread, they said to do it in the 'system'<br>
settings table, but the rowboat Android code checks the 'secure' settings).<br>
<br>
Here's what I did:<br>
<pre><code>adb shell<br>
# cd /data/data/com.android.providers.settings/databases<br>
# sqlite3 settings.db<br>
SQLite version 3.5.9<br>
Enter ".help" for instructions<br>
sqlite&gt; INSERT INTO secure (name,value) VALUES ('device_provisioned','1');<br>
sqlite&gt; .quit<br>
#<br>
</code></pre>

Then I rebooted. I don't know if this was required or not.<br>
<br>
After doing this, the HOME key worked with rowboat on OMAP3EVM.  Also, the<br>
keyguard comes on when a key is pressed after the screen has timed out.<br>
<br>
<h2>OMAP3EVM Touchscreen</h2>
[<a href='br.md'>br</a>]TBD:Add prerequisites<br>
<ul><li>To test touchscreen functionality we will use 2 applications from Google API Demo:<br>
FingerPaint and Touch Rotate. One uses simple 2D plane and touch tracing (paints red line) another uses more complex API with OpenGL ES usage.<br>
FingerPaint<br>
</li><li>Run FingerPaint from Menu -> API Demos -> Graphics -> FingerPaint. You will see grey background if the app runs successfully. Using board’s touchscreen paint some figures - square, triangle and circle. If you able to do this - test passed.<br>
Touch Rotate<br>
</li><li>Run Touch Rotate from Menu -> API Demos -> Graphics -> OpenGL ES -> Touch Rotate. You will see blue cube. Using touch and slide movements try to rotate the cube several times in different sides. If you able to do this - test passed.</li></ul>

<h2>Video modes for external display</h2>
Beagleboard and OMAP3EVM boards supports external display feature. This test case shows how you can check supported video modes (resolution and color depth).<br>
<br>
<ul><li>Beagleboard<br>
<table><thead><th> Mode </th><th> Resolution </th><th> Commandline </th><th> Support </th></thead><tbody>
<tr><td> HVGA </td><td> 480x320    </td><td> 480x320MR-16@60 </td><td> monitor error - not supported </td></tr>
<tr><td> VGA  </td><td> 640x480    </td><td> 640x480MR-16@60 </td><td> Yes     </td></tr>
<tr><td> VGA  </td><td> 480x640    </td><td> 480x640MR-16@60 </td><td> Yes. Android Desktop orientation is fine </td></tr>
<tr><td> SVGA </td><td> 800x600    </td><td> 800x600MR-16@60 </td><td> Yes     </td></tr>
<tr><td> XGA  </td><td> 1024x768   </td><td> 1024x768MR-16@60 </td><td> Yes     </td></tr>
<tr><td> SXGA </td><td> 1280x1024  </td><td> 1280x1024MR-16@60 </td><td> Yes     </td></tr>
<tr><td> WXGA (HD Ready) </td><td> 1280x720   </td><td> 1280x720MR-16@60 </td><td> Yes     </td></tr>
<tr><td> WSXGA </td><td> 1440x900   </td><td> 1440x900MR-16@60 </td><td> Yes     </td></tr>
<tr><td> SXGA| </td><td> 1440x1050  </td><td> 1440x1050MR-16@60 </td><td> Yes     </td></tr>
<tr><td> WSXGA </td><td> 1600x1024  </td><td> 1600x1024MR-16@60 </td><td> Yes     </td></tr>
<tr><td> UXGA </td><td> 1600x1200  </td><td> 1600x1200MR-16@60 </td><td> monitor error - not supported </td></tr>
<tr><td> FullHD </td><td> 1920x1080  </td><td> 1920x1080MR-16@60 </td><td> monitor error - not supported </td></tr>
<tr><td> WUXGA </td><td> 1920x1200  </td><td> 1920x1200MR-16@60 </td><td> monitor error - not supported</td></tr></li></ul></tbody></table>

<ul><li>OMAP3EVM<br>
<table><thead><th> Mode </th><th> Resolution </th><th> Commandline </th><th> Support </th></thead><tbody>
<tr><td> HVGA </td><td> 480x320    </td><td> 480x320MR-16@60 </td><td> TBD     </td></tr>
<tr><td> VGA  </td><td> 640x480    </td><td> 640x480MR-16@60 </td><td> TBD     </td></tr>
<tr><td> VGA  </td><td> 480x640    </td><td> 480x640MR-16@60 </td><td> TBD     </td></tr>
<tr><td> SVGA </td><td> 800x600    </td><td> 800x600MR-16@60 </td><td> TBD     </td></tr>
<tr><td> XGA  </td><td> 1024x768   </td><td> 1024x768MR-16@60 </td><td> TBD     </td></tr>
<tr><td> SXGA </td><td> 1280x1024  </td><td> 1280x1024MR-16@60 </td><td> TBD     </td></tr>
<tr><td> WXGA (HD Ready) </td><td> 1280x720   </td><td> 1280x720MR-16@60 </td><td> TBD     </td></tr>
<tr><td> WSXGA </td><td> 1440x900   </td><td> 1440x900MR-16@60 </td><td> TBD     </td></tr>
<tr><td> SXGA| </td><td> 1440x1050  </td><td> 1440x1050MR-16@60 </td><td> TBD     </td></tr>
<tr><td> WSXGA </td><td> 1600x1024  </td><td> 1600x1024MR-16@60 </td><td> TBD     </td></tr>
<tr><td> UXGA </td><td> 1600x1200  </td><td> 1600x1200MR-16@60 </td><td> TBD     </td></tr>
<tr><td> FullHD </td><td> 1920x1080  </td><td> 1920x1080MR-16@60 </td><td> TBD     </td></tr>
<tr><td> WUXGA </td><td> 1920x1200  </td><td> 1920x1200MR-16@60 </td><td> TBD     </td></tr></li></ul></tbody></table>

<h2>OMAP3EVM On-board display</h2>