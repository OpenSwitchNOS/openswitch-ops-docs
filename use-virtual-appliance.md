# Using OpenSwitch Virtual Appliance

- [Introduction](#introduction)
- [Setup](#setup)

## Introduction

OpenSwitch provides a Virtual Machine Appliance that uses a software data plane to forward the packets. The OpenSwitch appliance is not intended to be used as a vswitch in virtualization environments, but instead as a training and testing tool.

The appliance can by used to connect OpenSwitch with external hardware, like traditional networking gear or testing equipment without requiring access to the real equipment from the compatibility list.

## Setup

OpenSwitch Appliance VM is delivered in [OVA](https://en.wikipedia.org/wiki/Open_Virtualization_Format) format. This guide assumes the use of the VirtualBox solution since is freely available in all major platforms, but the image is reported to work as well with VMware products.

The first step is to download [VirtualBox](https://www.virtualbox.org) and install it on your machine.

Then proceed to download an OpenSwitch Appliance image (the lastest build from master branch is [here](https://archive.openswitch.net/artifacts/periodic/master/latest/appliance/)).

Import the OVA image into VirtualBox, and configure the networking setup:
* The appliance is configured with 4 network interfaces
* The first network interface is the Out Of Band Management (OOBM) port, and you may want to set it up as a bridged interface to your main network adapter to obtain and IP address via DHCP (this is the default behavior for the OOBM on OpenSwitch).
* The other three network interfaces from the appliance are the front ports of the virtual switch (called eth1, eth2, eth3 inside the switch). You can bridge these ports to network adapters (like an USB to eternet adapter) and have them connected
to traditional equipment.

After the setup is complete, powering the virtual machine should start a working OpenSwitch virtual image.
