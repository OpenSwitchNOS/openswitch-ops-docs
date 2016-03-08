Installing OpenSwitch on an AS5712 (OpenSwitch supports other switches as well – please see the Hardware compatibility list
and follow the more detailed installation guide here)
 
a. Download the OPS image at https://archive.openswitch.net/artifacts/periodic/master/latest/as5712/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016030812

b. If you have networking setup on your AS5712 and an accessible tftp server

   1. Copy the downloaded OPS image into the tftp directory with the name ops_as5712
   2. Telnet to the switch, reboot, get the switch into ONIE Rescue OS modd and enter these commands
   
        1. tftp –g –r ops_as5712 –l ops_5712 <tftp_server>
        2. chmod +x ops_as5712
        3. export OPS_CLEAN_INSTALL=true
        4. ./ops_5712
   3. Reboot and OPS will be booted up

c. If you don’t have networking setup, you can use a USB-to-serial as follows:

    For the below steps, Windows laptop running Windows 7 Enterprise is used.
    Prerequisites:
		
    1. USB to Serial adapter should be used.  For this case, Belkin Serial Adapter, model F5U257
       is used.  The corresponding driver for Windows 7 Enterprise can be found at
       http://www.belkin.com/us/support-article?articleNum=4644
    2. Connect the USB to serial adapter to the Windows laptop and install the driver.
    3. Make sure the new device shows up in Device Manager under
       Ports (COM & LPT) as Belkin USB-to-Serial-Adapter(COMx)
       x after COM will vary based on the USB port to which it is connected.
    4. Copy the onie installer openswitch-onie-installer-x86_64-as5712_54x-<>
       (eg.openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016030718)
       to a USB device.  This can be downloaded from https://archive.openswitch.net/artifacts/periodic/master/&lt;version&gt;/as5712/
    
    Follow these steps:
    
    1. Connect the windows laptop to 5712 switch using the USB to Serial adapter
    2. Using putty, choose below:
       Serial line : COM4
       Speed : 115200
    3. Once connected to the switch, reboot the switch
    
    4. Wait for the menu
    
    ```bash
    +----------------------------------------------------------------------------+
    | OpenSwitch Primary Image                                                   |
    | OpenSwitch Secondary Image                                                 |
    | OpenSwitch Development -- NFS root                                         |
    |*ONIE                                                                       |
    | DIAG: Accton Diagnostic                                                    |
    |                                                                            |
    +----------------------------------------------------------------------------+
    ```
    
    Select ONIE, then ONIE: Rescue
    
    ```bash
    +----------------------------------------------------------------------------+
    | ONIE: Install OS                                                           |
    |*ONIE: Rescue                                                               |
    | ONIE: Uninstall OS                                                         |                                                                            | ONIE: Update ONIE                                                          |
    | ONIE: Embed ONIE                                                           |
    |                                                                            |
    |                                                                            |
    +----------------------------------------------------------------------------+
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
    openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016030718
    
    mkdir /onie-installer/
    cp  /mnt/usb_drive_mount_point/openswitch-onie-installer-x86_64-as5712_54x-0.3.0+2016030718 /onie-installer/
    ```
    
    Run the installer:
    ```bash
    ONIE:/onie-installer # ./openswitch-onie-installer-x86_64-as5712_54x-0.3.0\+2016030718
    
    ONIE:/onie-installer # ./openswitch-onie-installer-x86_64-as5712_54x-0.3.0\+2016030718
    
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
    OpenSwitch 0.3.0 (Build: 0.3.0+2016030718-0.3.0-20160307180748-dev)
    switch#
    ```
d. Now that you have the switch booting to OpenSwitch:

   1. Telnet to the switch with these username/password
   2. Type “show”
   3. Try the following sample config for simple networking:
      1. conf t

e. Installing OpenSwitch virtual appliance on virtual box

   1. You can use an OpenSwitch virtual appliance to demo the control-plane features of OpenSwitch.
      While it is possible to get networking done through an OVA, it is beyond the scope of this manual to get the 
      required network driver mapping to work.
   2. Download the OVA image from 
      https://archive.openswitch.net/artifacts/periodic/master/latest/appliance/openswitch-appliance-image-appliance-0.3.0+2016030818.ova
   3. Click on File > "Import Appliance" and select the OVA file downloaded above
   4. Click "Continue" and then "Import". This completes the importing of OVA file into your VirtualBox.
   5. Click on the VM instance you've just imported. It should show up as "OpenSwitch-0.3.0.*". Click "Start > 'Normal Start' ".
      This will open a GUI window where you will notice that the OpenSwitch instance is booting up and launching.
   6. Once it has launched it would show you a prompt "ops-appliance login:". This is the switch login prompt. 
      Please enter 'root' and you should be able to see a bash prompt.
   7. Enter 'vtysh' and it should take you to the switch prompt. Here you can access all the privilege mode 
      commands and would be able to issue all the 'show' commands. 
   8. Enter "configure terminal" and you should be in the configuration mode where you can try out different 
      configurations like configuring OSPF/BGP/NTP etc. User guides for different features can be found here
      http://openswitch.net/use/usehome
