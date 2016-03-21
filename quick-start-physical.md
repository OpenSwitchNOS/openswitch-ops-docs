# Quick Start Guide to install OpenSwitch

- [Finding an OpenSwitch Image](#finding-an-openswitch-image)
- [Installing OpenSwitch on an AS5712](#installing-openswitch-on-an-as5712)

## Finding an OpenSwitch Image

For using latest release image, use ONIE installer and image from [this workspace](https://archive.openswitch.net/artifacts/periodic/release/latest/as5712/).

For using latest image from master branch, follow the following steps: (Note: Only do if you're interested in the newer features which are actively developed in master branch)
   1. We'll use the [test_results_analyzer](https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/test_results_analyzer/) to get information on a stable build.
   2. Click on "Get Build Report" and "Select chart type" -> "bar". This shows at a glance, which builds are most stable and have run maximum number of passed testcases.
   3. Pick the build number which you'll like to use, for eg: 823 is the most stable build at the time of writing.
   4. Now use this build number and go to `https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/<build_number>/testResults`. This testResults page displays all the tests which were run on this periodic build. For example: [build_823_test_results](https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/823/testResults)
   5. Use testResults information you get a clear idea of which features are tested with this periodic build. If this build does not satisfy your requirements, please choose another build and repeat all of the above steps.
   6. Note the timestamp of the periodic build you've chosen from `https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/<build_number>/` . You'll need this info to continue Install on AS5712 or using it as a virtual appliance.

## Installing OpenSwitch on an AS5712

This guide will use AS5712 as an example. Please see [hw compatible](/documents/user/hardware-compatibility) guide for other platforms.

a. Using the timestamp you've noted from "Finding an OpenSwitch Image" step, download the OPS ONIE installer image from [periodic_artifacts](https://archive.openswitch.net/artifacts/periodic/master/) from the 0.3.0+&lt;YYYYMMDDHH&gt;/as5712/ folder. For example: [download_build_823_onie_installer](https://archive.openswitch.net/artifacts/periodic/master//0.3.0+2016031100/as5712/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100)

b. If you have networking setup on your AS5712 and an accessible tftp server
   1. Copy the downloaded OPS image into the tftp directory with the name ops_as5712
   2. Telnet to the switch, reboot, get the switch into ONIE Rescue OS mode and enter these commands
        ```
        1. tftp –g –r ops_as5712 –l ops_5712 <tftp_server>
        2. chmod +x ops_as5712
        3. export OPS_CLEAN_INSTALL=true
        4. ./ops_5712
        ```
   3. Reboot and OPS will be booted up

c. If you don’t have networking setup, follow the below steps
     (for the below steps, Windows laptop running Windows 7 Enterprise is used)

1. USB to Serial adapter should be used.  For this case, Belkin Serial Adapter, model F5U257 is used.  The corresponding driver for Windows 7 Enterprise can be found at http://www.belkin.com/us/support-article?articleNum=4644
2. Connect the USB to serial adapter to the Windows laptop and install the driver.
3. Make sure the new device shows up in Device Manager under Ports (COM & LPT) as Belkin USB-to-Serial-Adapter(COMx) x after COM will vary based on the USB port to which it is connected.
4. Copy the OPS onie installer image to a USB device.  Use the installer you've identified in Step a above.
5. Connect the windows laptop to AS5712 switch using the USB to Serial adapter
6. Using putty, choose Serial line as COM<> and Speed as 115200
7. Once connected to the switch, reboot the switch

8. Wait for the menu
    ```bash
    +----------------------------------------------------------+
    | OpenSwitch Primary Image                                 |
    | OpenSwitch Secondary Image                               |
    | OpenSwitch Development -- NFS root                       |
    |*ONIE                                                     |
    | DIAG: Accton Diagnostic                                  |
    |                                                          |
    +----------------------------------------------------------+
    ```

      Select ONIE, then ONIE: Rescue

    ```bash
    +---------------------------------------------------------+
    | ONIE: Install OS                                        |
    |*ONIE: Rescue                                            |
    | ONIE: Uninstall OS                                      |
    | ONIE: Embed ONIE                                        |
    |                                                         |
    |                                                         |
    +---------------------------------------------------------+
    ```
    In the ONIE prompt, run fdisk -l as below

    ```bash

     ONIE:/ #

     ONIE:/ # fdisk -l

    Disk /dev/sda: 8048 MB, 8048869376 bytes
    100 heads, 38 sectors/track, 4136 cylinders
    Units = cylinders of 3800 * 512 = 1945600 bytes

       Device Boot      Start         End      Blocks  Id System
    /dev/sda1               1        4137     7860223+ ee EFI GPT

    Disk /dev/sdb: 258 MB, 258998272 bytes
    255 heads, 63 sectors/track, 31 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes

       Device Boot      Start         End      Blocks  Id System
    /dev/sdb1   *           1          32      252896+  c Win95 FAT32 (LBA)
    Partition 1 has different physical/logical endings:
         phys=(30, 254, 63) logical=(31, 124, 29)
    ```

    Pick the partition that has the flash drive inserted (/dev/sdb1 in this case) and mount it:
    ```bash
    ONIE:/ # mkdir /mnt/usb_drive_mount_point
    ONIE:/ # mount /dev/sdb1 /mnt/usb_drive_mount_point/
    ```

    Copy the onie installer file to a folder
    ```bash
    ONIE:/ # ls /mnt/usb_drive_mount_point/
    openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100

    mkdir /onie-installer/
    cp  /mnt/usb_drive_mount_point/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100 /onie-installer/
    ```

    Run the installer:
    ```bash
    ONIE:/onie-installer # ./openswitch-onie-installer-x86_64-as5712_54x-0.3.0\+2016031100

    ONIE:/onie-installer # ./openswitch-onie-installer-x86_64-as5712_54x-0.3.0\+2016031100

    OpenSwitch ONIE installer (version 1.0) for Accton AS5712 54x

    Initializing Intel(R) Boot Agent GE v1.5.43
    PXE 2.1 Build 092 (WfM 2.0)
    Press Ctrl+S to enter the Setup Menu..

    --- Installing OpenSwitch GRUB ---
    Installation finished. No error reported.

    OpenSwitch installation completed

    Rebooting...
    ```

    Once rebooted, login as root and check the version.  It should match with the one that was installed:
    ```bash
    root@switch:~# vtysh
    switch# show version
    OpenSwitch 0.3.0 (Build: 0.3.0+20160311008-0.3.0-20160311001048-dev)
    switch#
    ```

    Now ip address details can be configured for telnet access to the switch.

d. Now that you have the switch booting to OpenSwitch:

   1. Telnet to the switch and login as root. Currently no password is set for root.
   2. You will notice a bash prompt. Now enter "vtysh"
   3. After entering vtysh, type "show ?". You should be able to see a bunch of familiar show commands for different features.
   4. Try the following sample config for simple networking:
      1. conf t
      2. ip route &lt;destination&gt; &lt;nexthop | interface&gt; [&lt;distance&gt;]
      3. detailed reference can be found at http://openswitch.net/use/usehome
   5. Configure mgmt interface using step-by-step config guide of [mgmt-intf](/documents/user/mgmt_intf_cli)
   6. You should be able to login through the web-ui by using ip address obtained from step 9. Open a browser and type that ip address and press enter. Fore more details refer [here](/documents/user/webui_user_guide) for accessing web-ui.
   7. Login for web-ui can be done using username:root and no password.

  Using REST:
  ===========
  Before moving forward to this section, confirm that the OpenSwitch instance is UP and Running through the VirtualBox dashboard. Also confirm 'ifconfig' output from the bash prompt within the OpenSwitch instance that it has valid IP address. If this criteria is not met, then please refer to troubleshooting guide for more info.
  1. Login to the switch and note the IPAddress of the switch. By issuing 'ifconfig' in the bash prompt you should see an output like this:
    ```
    root@switch:# ifconfig
    eth0  Link encap:Ethernet HWaddr 08:12:24:12:14:84
          inet addr:192.168.2.19 Bcast:192.168.2.255 Mask:255.255.255.0
    ```
  2. REST documentation is hosted by OpenSwitch instance. So its very useful and easy to find it by going to http://IPAddress:8091/api/index.html. In this example: http://192.168.2.19:8091/api/index.html gives you the complete REST documentation.
  3. Using this portal you can try out different REST GET/PUT/POST calls. It would work directly by posting/getting information from the OVSDB. For more details refer to usage guides.

  Using WebUI:
  ============
  The webUI service on the switch is running on the standard port 80. Once the management interface is configured and has a valid IP address, point your web browser to this IP address.
