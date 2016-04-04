# Introduction #

This page will discuss using the pre-built Rowboat Android images to try out Rowboat quickly without having to download and build the source (hopefully within minutes!). There are a couple of ways to do this, primarily an SD card boot or a network TFTP/NFS based boot.


# Preparing mmc/sd card for reflashing boards and booting Android #

  * Reflashing
If you are unsure about state of bootloaders and environment variables as one of the simplest steps we recommend to try **reflashing** procedure. It will update bootloaders and its settings. Start with reflash SD card preparation, then modify switches and boot to get your board updated
  * Creating Android SD card
If you are unsure about creation of SD card to boot Android we recommend to try preparation script for this.

To create MMC/SD card for reflashing or booting Android on Beagleboard or OMAP3EVM boards with the x-load and u-boot we provide use mk-mmc scripts located here: http://rowboat.googlecode.com/files/mk-mmc.tar.gz. Also you can get it from the git repository: `git clone git://gitorious.org/rowboat/mk-mmc.git`

See detailed instructions on using mk-mmc scripts in the README file provided in the tarball and repository.

# Booting Rowboat via TFTP/NFS on the Mistral EVM #

This is the boot of choice particularly if you already have a Mistral EVM up and running with NFS after following the [OMAP3 EVM Getting Started Guide](http://wiki.davincidsp.com/index.php/OMAP35x_DVEVM_Getting_Started_Guide). Before using this method, please note that this method assumes you are using a Linux host machine to run a TFTP and NFS servers, most any typical PC distribution will do, though this was tested against Ubuntu.

  * First you can download the [pre-built images here](http://code.google.com/p/rowboat/downloads/list). There are several available files here for download, if you are unsure what to pick just choose the latest 'daily build' for your platform (for example omap3evm-donut-20xx-xx-xx.tar.bz2).
  * Now you can move the file to your preferred working directory and un-tar the file to take a look at the contents. This should give you two files, uImage which contains your OS kernel and another tar file which contains the file system (for example omap3evm-donut-rootfs-20xx-xx-xx.tar.bz2).
`host % tar -jxvf omap3evm-donut-20xx-xx-xx.tar.bz2`
  * If you have already been working with your OMAP3 EVM you probably already have some of the background work for setting up a TFTP and NFS server on your host done, however if you have not you can read about setting up a [TFTP server here](http://wiki.davincidsp.com/index.php/Setting_Up_a_TFTP_Server#Using_Ubuntu) and a [NFS server here](http://tiexpressdsp.com/index.php/GSG:_DVSDK_2.0_Software_Setup_in_Ubuntu#Exporting_a_Shared_File_System_for_Target_Access). With the TFTP and NFS servers up and ready to go we simply need to put the Rowboat files into place and configure the board for NFS.
  * For the kernel image file uImage one must copy the file to the TFTP server's tftpboot directory, in the case of Ubuntu this is generally /var/lib/tftpboot. Note that by default this directory is protected so the sudo command will be used to authorize the copy.
`host % sudo cp uImage /var/lib/tftpboot`
  * For the filesystem we can extract this anywhere and than configure the NFS server to serve it, so if it is already in a directory you wish to work from you can just extract it here. Note we are using the sudo command for full privelages to guarantee the file system is extracted properly.
`host % sudo tar -jxvf omap3evm-donut-rootfs-20xx-xx-xx.tar.bz2`
  * Now that we have the file system extracted we need to configure the NFS server to serve it, so we will edit the /etc/exports file to add a line like `/your/particular/host/path/omap3evm-donut-rootfs-20xx-xx-xx *(rw,no_root_squash,no_all_squash,sync)` and save the file. You can use any editor to do this that you like, but if you are new to Linux than gedit is easy, note that once again we are using sudo to allow us to edit this file.
`host % sudo gedit /etc/exports`
  * Now that the NFS is configured you need to restart the service so it will add in the new directory, so a command like below should do it, note this may vary based on the flavor of NFS server you installed earlier.
`host % sudo service nfs-kernel-server restart`
  * With this the host Linux PC should be sharing both the uImage kernel and the filesystem, so we need to configure the U-Boot environment variables. Power on your board and interrupt the U-Boot sequence and run the following commands. Note that you need to use the IP address of your host Linux machine (in place of the xxx...), and the actual path to your NFS filesystem in the U-Boot variables you are setting here.
`EVM % setenv bootcmd 'dhcp; bootm'` <br>
<code>EVM % setenv bootargs 'mem=128M console=ttyS0,115200n8 androidboot.console=ttyS0 noinitrd ip=dhcp rw root=/dev/nfs nfsroot=xxx.xxx.xxx.xxx:/your/particular/host/path/omap3evm-donut-rootfs-20xx-xx-xx,nolock,proto=tcp init=/init rootwait'</code><br>
<code>EVM % setenv serverip xxx.xxx.xxx.xxx</code><br>
<code>EVM % setenv bootfile uImage</code><br>
<code>EVM % saveenv</code>
<ul><li>Now that you have your U-Boot environment configured you can either power cycle the board or type boot to start the booting process of Rowboat. If all is well you should see a bunch of text come out on the terminal and eventually an Android logo on the LCD of the EVM. Please be patient while Rowboat Android boots for the first time, there is a lot of initial configuration that goes on during the first boot and it can take a couple of minutes. When it is finished the LCD should display a phone like screen, and you should be able to interact with it through the touch screen, buttons S4 and S7 will act as menu and back buttons.<br>
</li><li>Congratulations, you are now running Rowboat Android on your EVM!