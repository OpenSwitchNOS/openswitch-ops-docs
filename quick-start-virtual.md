# Using OpenSwitch Virtual Appliance

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installing OpenSwitch virtual appliance on VirtualBox](#installing-openswitch-virtual-appliance-on-virtualbox)
- [Networking Setup](#networking-setup)
- [Using the REST API](#using-the-rest-api)
- [Using the web UI](#using-the-web-ui)

## Introduction

OpenSwitch provides a Virtual Machine Appliance that uses a software data plane to forward the packets. The OpenSwitch appliance is not intended to be used as a virtual switch in virtualization environments, but instead as a training and testing tool.

The appliance can by used to connect OpenSwitch to external hardware, such as traditional networking gear or testing equipment without requiring access to the real equipment from the compatibility list.

## Prerequisites

OpenSwitch Appliance VM is delivered in [OVA](https://en.wikipedia.org/wiki/Open_Virtualization_Format) format. This guide assumes the use of the VirtualBox solution since is freely available on all major platforms; however, the image is also reported to work with VMware products.
  1. Download [VirtualBox](https://www.virtualbox.org) and install it on your computer.
  2. Download an OpenSwitch Appliance image (the lastest build from master branch is [here](https://archive.openswitch.net/artifacts/periodic/master/latest/appliance/)) to your computer.

## Installing OpenSwitch virtual appliance on VirtualBox

You can use an OpenSwitch virtual appliance to demo the control-plane features of OpenSwitch. While it is possible to obtain networking information through an OVA file, it is beyond the scope of this manual to configure the required network driver.

   1. After you have selected a stable OpenSwitch image. use [periodic_appliance](https://archive.openswitch.net/artifacts/periodic/master/) and locate 0.3.0+&lt;YYYYMMDDHH&gt;/appliance/openswitch-appliance-image-appliance-0.3.0+&lt;YYYYMMDDHH&gt;.ova file. For example:  [download_latest_appliance](https://archive.openswitch.net/artifacts/periodic/master/latest/)
   2. On the VirtualBox, click **File** > **Import Appliance** and select the OVA file that was downloaded as part of the previous step.
   3. Click **Continue** and then **Import**. This action completes the importing of an OVA file into your VirtualBox.
   4. Click the VM instance you have just imported, such as "OpenSwitch-0.3.0 Appliance".
   5. Click **Start** > **Normal Start**.
      This action opens a GUI window displaying the OpenSwitch instance booting up and launching.
   6. Once the OpenSwitch instance has launched, the instance shows you a prompt switch login:
      `Please enter 'root' and you should be able to see a bash prompt.`
   7. Enter `vtysh` and the switch should take you to the switch prompt. Here you can access all the privilege mode commands, in addition to being able to issue the `show` commands.
   8. Enter `configure terminal`.
      You are in the configuration mode where you can try out different configurations, such as configuring OSPF/BGP/NTP. The user guides for the different features can be found [here](http://openswitch.net/use/usehome).
   9. Obtain the IP address for the switch by entering an `ifconfig` command at the Bash prompt and finding the IP Addresses used on eth0 interface.
   10. Log in to the web UI by using the IP address obtained from step 9. Open a browser and type the IP address and press enter. Fore more details, click [here](/documents/user/webui_user_guide) for accessing the web UI. Log in to the web UI by providing  `root` for the username and no password.
   11. To add a password, click root at the bottom of the left pane, and select **Change Password**. Then, provide a new password and click **Change**.

## Networking Setup
* The appliance is configured with 4 network interfaces.
* The first network interface is the Out Of Band Management (OOBM) port. You might want to set it up as a bridged interface to your main network adapter to obtain an IP address via DHCP. This behavior is the default for the OOBM on OpenSwitch.
* The other three network interfaces from the appliance are the front ports of the virtual switch (called eth1, eth2, eth3 inside the switch). You can bridge these ports to network adapters (such as a USB to eternet adapter) and have them connected to traditional equipment.
* After the setup is complete, powering the virtual machine should start a working OpenSwitch virtual image.

## Using the REST API

Before you read this section, confirm that the VirtualBox dashboard shows the OpenSwitch instance up and running. Also, confirm that `ifconfig` output for the OpenSwitch instance has a valid IP address. If this criteria is not met, see the troubleshooting guide for more information.

  1. Log in to the switch.
  2. Enter `ifconfig` in the bash prompt:
    ```
    root@switch:# ifconfig
    eth0  Link encap:Ethernet HWaddr 08:12:24:12:14:84
          inet addr:192.168.2.19 Bcast:192.168.2.255 Mask:255.255.255.0
    ```
     The IP address for the switch displayed next to the 'inet addr' heading.
  3. REST documentation is hosted by an OpenSwitch instance. To find the REST documentation, go to https://IPAddress/api/index.html, for example: https://192.168.2.19/api/index.html.
  4. You can try out different REST GET/PUT/POST calls by using this portal. Login to the switch webUI (https://IPAddress/) before executing REST calls. It works directly by posting/getting information from the OVSDB. For more details refer to usage guide for each feature.

## Using the web UI

The web UI on the switch runs on standard port 80. Once the management interface is configured and it has a valid IP address, point your web browser to this IP address, for example: https://192.168.2.19.
