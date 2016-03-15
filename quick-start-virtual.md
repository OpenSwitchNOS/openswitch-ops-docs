# Using OpenSwitch Virtual Appliance

- [Introduction](#introduction)
- [Setup](#setup)
- [Installing OpenSwitch virtual appliance on VirtalBox](#installing-openswitch-virtual-appliance-on-virtualbox)
- [Networking Setup](#networking-setup)

## Introduction

OpenSwitch provides a Virtual Machine Appliance that uses a software data plane to forward the packets. The OpenSwitch appliance is not intended to be used as a vswitch in virtualization environments, but instead as a training and testing tool.

The appliance can by used to connect OpenSwitch with external hardware, like traditional networking gear or testing equipment without requiring access to the real equipment from the compatibility list.

## Setup

OpenSwitch Appliance VM is delivered in [OVA](https://en.wikipedia.org/wiki/Open_Virtualization_Format) format. This guide assumes the use of the VirtualBox solution since is freely available in all major platforms, but the image is reported to work as well with VMware products.

The first step is to download [VirtualBox](https://www.virtualbox.org) and install it on your machine.

Then proceed to download an OpenSwitch Appliance image (the lastest build from master branch is [here](https://archive.openswitch.net/artifacts/periodic/master/latest/appliance/)).

## Installing OpenSwitch virtual appliance on VirtualBox

   1. You can use an OpenSwitch virtual appliance to demo the control-plane features of OpenSwitch.
      While it is possible to get networking done through an OVA, it is beyond the scope of this manual to get the
      required network driver mapping to work.
   2. After you've selected a stable OpenSwitch image. Please use [periodic_appliance](https://archive.openswitch.net/artifacts/periodic/master/) and locate 0.3.0+&lt;YYYYMMDDHH&gt;/appliance/openswitch-appliance-image-appliance-0.3.0+&lt;YYYYMMDDHH&gt;.ova file. For example:  [download_build_823_appliance](https://archive.openswitch.net/artifacts/periodic/master/0.3.0+2016031100/appliance/openswitch-appliance-image-appliance-0.3.0+2016031100.ova)
   3. On the VirtualBox, click on File > "Import Appliance" and select the OVA file downloaded above
   4. Click "Continue" and then "Import". This completes the importing of OVA file into your VirtualBox.
   5. Click on the VM instance you've just imported. It should show up as "OpenSwitch-0.3.0 Appliance".
      Click "Start > 'Normal Start' ".
      This will open a GUI window where you will notice that the OpenSwitch instance is booting up and launching.
   6. Once it has launched it would show you a prompt "switch login:". This is the switch login prompt.
      Please enter 'root' and you should be able to see a bash prompt.
   7. Enter 'vtysh' and it should take you to the switch prompt. Here you can access all the privilege mode
      commands and would be able to issue all the 'show' commands.
   8. Enter "configure terminal" and you should be in the configuration mode where you can try out different
      configurations like configuring OSPF/BGP/NTP etc. User guides for different features can be found here
      http://openswitch.net/use/usehome
   9. Get ipaddress from the switch by issuing 'ifconfig' on bash prompt and finding IP Addresses used on eth0 interface.
   10. You should be able to login through the web-ui by using ip address obtained from step 9. Open a browser and type that ip address and press enter. Fore more details refer [here](documents/user/webui_user_guide) for accessing web-ui.
   11. Login for web-ui can be done using username:root and no password.

## Networking Setup:
* The appliance is configured with 4 network interfaces
* The first network interface is the Out Of Band Management (OOBM) port, and you may want to set it up as a bridged interface to your main network adapter to obtain and IP address via DHCP (this is the default behavior for the OOBM on OpenSwitch).
* The other three network interfaces from the appliance are the front ports of the virtual switch (called eth1, eth2, eth3 inside the switch). You can bridge these ports to network adapters (like an USB to eternet adapter) and have them connected
to traditional equipment.

After the setup is complete, powering the virtual machine should start a working OpenSwitch virtual image.

  Using REST:
  ===========
  Before moving forward to this section, confirm that the OpenSwitch instance is UP and Running through the VirtualBox dashboard. Also confirm 'ifconfig' output from the bash prompt within the OpenSwitch instance that it has valid IP address. If this criteria is not met, then please refer to troubleshooting guide for more info.
  1. Login to the switch and note the IPAddress of the switch. By issuing 'ifconfig' in the bash prompt you should see an output like this:
  root@switch:# ifconfig
  eth0  Link encap:Ethernet HWaddr 08:12:24:12:14:84
      inet addr:192.168.2.19 Bcast:192.168.2.255 Mask:255.255.255.0
          .....
          2. REST documentation is hosted by OpenSwitch instance. So its very useful and easy to find it by going to http://IPAddress:8091/api/index.html. In this example: http://192.168.2.19:8091/api/index.html gives you the complete REST documentation.
          3. Using this portal you can try out different REST GET/PUT/POST calls. It would work directly by posting/getting information from the OVSDB. For more details refer to usage guides.

  Using WebUI:
  ============
  The webUI service on the switch is running on the standard port 80. Once the management interface is configured and has a valid IP address, point your web browser to this IP address.
