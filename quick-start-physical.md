# Quick Start Guide to installing OpenSwitch

- [Finding an OpenSwitch image](#finding-an-openswitch-image)
- [Installing OpenSwitch](#installing-openswitch)

## Finding an OpenSwitch image

### Obtaining the latest released image
Use the ONIE installer and image from [this workspace](https://archive.openswitch.net/artifacts/periodic/release/latest/as5712/).

### Obtaining the latest image from the master branch
Obtain the latest image from the master branch only if you are interested in the newer features, which are actively developed in the master branch.

To obtain the latest image from the master branch:
1. Go to [test_results_analyzer](https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/test_results_analyzer/). Test analyzer is used to obtain information on a stable build.
2. From the Select chart type menu, select **Bar**.
3. Click **Generate Charts**. Then, click **Get Build Report**.
3. From the heading of the bar chart, pick the build number that you want to use, for example build 823.
4. Write down the build number from the previous step.
5. The Test Results page displays all the tests that were run on this periodic build. To access the Test Results page for a build, go to `https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/<build_number>/testResults`.  Replace `build_number` with the build number you wrote down from the previous step. For example: `https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/823/testResults`
5. Use the information from the Test Results page to give you a clear idea which features are tested with this build. If this build does not satisfy your requirements, choose another build and repeat the above steps.
6. Note the timestamp of the periodic build have chosen from `https://jenkins.openswitch.net/job/ops-periodic-genericx86-64/<build_number>/` .
7. You will need this info to continue to install the software on the switch or to use it as a virtual appliance.

## Installing OpenSwitch

This guide uses the AS5712 switch as an example. Please see the [Hardware Compatibility](/documents/user/hardware-compatibility) guide for other platforms.
1.[Download the OPS ONIE installer image](#downloading-the-ops-onie-installer-image).
2.For switches with networking already setup and accessible to a TFTP server, see [Installing OpenSwitch on switches accessible to a network](#installing-openSwitch-on-switches-accessible-to-a-network).
3.For switches without a network setup, see [Installing OpenSwitch on switches not accessible to a network](#installing-openSwitch-on-switches-not-accessible-to-a-network).


### Downloading the OPS ONIE installer image
Using the timestamp you have noted from "Finding an OpenSwitch Image" step, download the OPS ONIE installer image that has a similar timestamp from [periodic_artifacts](https://archive.openswitch.net/artifacts/periodic/master/) from the ``0.3.0+&lt;YYYYMMDDHH&gt;/as5712/`` folder.
For example: (https://archive.openswitch.net/artifacts/periodic/master/0.3.0+2016031100/as5712/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100)

### Installing OpenSwitch on switches accessible to a network
These steps are for switches with networking setup and accessible to a TFTP server.
To install OpenSwitch:
1. Copy the downloaded OPS image into the TFTP directory with the name ops_as5712.
2. Telnet to the switch.
3. Reboot and configure the switch into the ONIE Rescue OS mode.
4. Enter the following command command:
   ```
   tftp –g –r ops_as5712 –l ops_5712 <tftp_server>
   ```
5. Enter the following command:
   ```
   chmod +x ops_as5712
   ```
6. Enter the following command:
   ```
   export OPS_CLEAN_INSTALL=true
   ```
7. Enter the following command:
   ```
   ./ops_5712
   ```
8. Reboot the switch. OpenSwitch is booted up.

### Installing OpenSwitch on switches not accessible to a network
The following steps were tested on a Windows laptop running Windows 7 Enterprise.
1. Find a USB to serial adapter, such as the Belkin Serial Adapter, model F5U257.
2. Download the corresponding driver for the adapter. If you used the Belkin Serial Adapter, mentioned in the previous step, its corresponding driver for Windows 7 Enterprise is at [http://www.belkin.com/us/support-article?articleNum=4644](http://www.belkin.com/us/support-article?articleNum=4644).
3. Connect the adapter to the Windows laptop and install the driver.
4. Make sure the new device appears in Device Manager under Ports (COM & LPT). For example, with the Belkin USB-to-Serial-Adapter(COMx), the x after "COM" varies based on the USB port to which it is connected.
5. Copy the OpenSwitch ONIE installer image to a USB device. Use the installer you downloaded by using the instructions in [Downloading the OPS ONIE installer image](#downloading-the-ops-onie-installer-image).
6. Connect the Windows laptop to the AS5712 switch by using the USB to serial adapter.
7. Using Putty, choose the serial line as **COM<>** and the speed as 115200.
8. Once you have connected to the switch, wait for the menu.
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

10. Select **ONIE**, then **ONIE: Rescue**.

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

11. In the ONIE prompt, run `fdisk -l`:

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

12. Pick the partition that has the flash drive inserted (/dev/sdb1 in this case) and mount it:
    ```bash
    ONIE:/ # mkdir /mnt/usb_drive_mount_point
    ONIE:/ # mount /dev/sdb1 /mnt/usb_drive_mount_point/
    ```

13. Copy the ONIE installer file to a folder:
    ```bash
    ONIE:/ # ls /mnt/usb_drive_mount_point/
    openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100

    mkdir /onie-installer/
    cp  /mnt/usb_drive_mount_point/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016031100 /onie-installer/
    ```

14. Run the installer:
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
15. Once the switch is rebooted, login as root and check the version. It should match with the one that was installed:

    ```bash
    root@switch:~# vtysh
    switch# show version
    OpenSwitch 0.3.0 (Build: 0.3.0+20160311008-0.3.0-20160311001048-dev)
    switch#
    ```
IP address details can now be configured for Telnet access to the switch. See [Configuring networking on the switch](#configuring-networking-on-the-switch).

### Configuring networking on the switch

1. Telnet to the switch and login as root. Currently no password is set for root.
2. At the Bash prompt, enter:
   ```bash
   vtysh
   ```
3. Enter:
  ```
  show ?
  ```
  You are shown show commands for different features.
4. Try the following sample config for simple networking:
   a. Enter:
     ```conf t```
   b. Enter:
     ```ip route <destination> <nexthop | interface> [<distance>]```
   c. Enter:
   ```detailed reference can be found at http://openswitch.net/use/usehome```
5. Configure the management interface using step-by-step config guide [mgmt-intf](/documents/user/mgmt_intf_cli).
6. You should be able to log in through the web-UI by using the IP address obtained from step 9. Open a browser and type that IP address and press enter. For more details refer to the [Web UI User Guide](/documents/user/webui_user_guide) for accessing the web-UI.
7. Log into web-UI by using username:root and no password.

## REST and the WebUI

### Prerequistes
* Confirm that the OpenSwitch instance is up and running by using the VirtualBox dashboard.
* Obtain the IP address of the switch, which can be found by entering `ifconfig`. The IP address for the switch is listed as inet addr.
```
bash-4.3# ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02
          inet addr:172.17.0.2  Bcast:0.0.0.0  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:28 errors:0 dropped:0 overruns:0 frame:0
          TX packets:22 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:4774  TX bytes:1704
```

### Using REST

  1. The REST documentation is hosted by the OpenSwitch instance. To find the documentation for REST, use the URL `https://<IPAddress>/api/index.html`. For example, `https://192.168.2.19/api/index.html`
  2. Using this portal you can try out different REST GET/PUT/POST calls. Login to the switch webUI (https://IPAddress/) before executing REST calls. It works directly by posting/getting information from the OVSDB. For more details, refer to usage guides.

### Using the web UI:
  The web UI service on the switch is running on the standard port 80. Once the management interface is configured and it has a valid IP address, point your web browser to this IP address.

For more details on installing OpenSwitch on physical hardware, refer to [Installing and Booting OpenSwitch](deploy-to-physical-switch)

#### More Information
[Troubleshooting the configuration](http://openswitch.net/documents/user/mgmt_intf_user_guide#troubleshooting-the-configuration)
