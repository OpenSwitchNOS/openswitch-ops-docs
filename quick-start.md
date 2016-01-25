# Quick Start Guide Using Vagrant

## Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installing the OpenSwitch development environment](#installing-the-openswitch-development-environment)
- [Connecting to the OpenSwitch instance](#connecting-to-the-openswitch-instance)
- [Build system and development environment](#build-system-and-development-environment)
- [Developing for OpenSwitch resources](#developing-for-openswitch-resources)

## Overview
The following guide describes an easy way to set up your development environment for the OpenSwitch project using  [VirtualBox](https://www.virtualbox.org/), [Vagrant](https://www.vagrantup.com/), and [Docker](https://www.docker.com).  The Vagrant image includes an Ubuntu VM with Docker already installed. Docker is used to run the OpenSwitch project image on a Linux container.

This approach has been tested on Windows 7 with Vagrant 1.7.4 and VirtualBox 5.0.

This guide shows how to set up a development environment with the following components:
* A VM running Ubuntu with the OpenSwitch development environment.
* An OpenSwitch x86_64 image built and ready to use.
* An instance of OpenSwitch (the x86_64 image) running inside a Docker container.

**Note:** The default username/password for the VM is vagrant/vagrant.

## Prerequisites
1. Download and install [VirtualBox](https://www.virtualbox.org/).
2. Download and install [Vagrant](https://www.vagrantup.com/).
3. If you are behind a proxy, make sure that the http_proxy and https_proxy environment variables are set in the command prompt or shell from which the commands to install the vagrant plugins (steps 4 and 5 below) are executed.
4. Install the required Vagrant plugins. Use the following command:
```
$ vagrant plugin install vagrant-reload
```
5. If you are behind a proxy, install the `vagrant-proxyconf` plugin:
```
$ vagrant plugin install vagrant-proxyconf
```

## Installing the OpenSwitch development environment

1. Download the Vagrant files from [here](https://archive.openswitch.net/vagrant/ops-vagrant.zip), and unzip them into a workspace directory.

2. If you are behind a proxy, set the proxy `host:port` information in the `host/Vagrantfile`. The `host/Vagrantfile` is located in the workspace directory where you unzipped the Vagrant files.
```
config.proxy.http = "http://proxy.example.com:8080"
config.proxy.https = "https://proxy.example.com:8080"
```
3. Browse to the workspace directory and run `vagrant up`.
```
$ cd openswitch-vagrant-master
$ vagrant up
```
**Note:** The first `vagrant up` process can take a while since the entire OpenSwitch repository is fetched and built.

## Connecting to the OpenSwitch instance
After following the steps in the above *"Installing the OpenSwitch development environment"* section, an OpenSwitch image is built and Docker is configured to run that OpenSwitch image. Use the following steps to connect to OpenSwitch. The following steps are performed in the newly created Ubuntu VM.

1. Run a shell on the Docker instance with `docker exec -ti ops bash`.
2. Run `vtysh` to start the OpenSwitch CLI.
3. The `vtysh` session times out if the session is idle for the session timeout period. The session timeout can be configured using the cli command `session-timeout <value>` in configure terminal mode (the default is 30 minutes).

Example:

```
vagrant@ops-host:~/ops-build$ docker exec -ti ops bash
bash-4.3# vtysh
2015-09-01T23:31:47Z|00001|reconnect|INFO|unix:/var/run/openvswitch/db.sock: connecting...
2015-09-01T23:31:47Z|00002|reconnect|INFO|unix:/var/run/openvswitch/db.sock: connected
```

## Build system and development environment
After following the above instructions, the newly created Ubuntu VM is fully installed with the OpenSwith Build System and Development Environment. The OpenSwitch Build System creates and maintains a Git repository that is cloned to `~/ops-build`. The Git repository contains a subset of the project's source files.


## Developing for OpenSwitch resources
For more information on developing for OpenSwitch, see the following guides:
* [Changing OpenSwith Code](changing-openswitch-code) to learn how to work with the OpenSwitch development environment.
* [How to Contribute Code](contribute-code) to learn how to submit your code to UpStream.
