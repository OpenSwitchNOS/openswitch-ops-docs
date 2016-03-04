# Step-by-Step Guide

## Contents
- [Overview](#overview)
- [Setting up the environment](#setting-up-the-environment)
- [Cloning OpenSwitch](#cloning-openswitch)
- [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system)
- [Configuring an OpenSwitch product](#configuring-an-openswitch-product)
- [Building the product](#building-the-product)
- [Deploying an image](#deploying-an-image)
	- [Install Docker on host machine](#install-Docker-on-host-machine)
	- [Simulated switch image](#simulated-switch-image)
	- [Mininet based testing](#mininet-based-testing)
- [Working with OpenSwitch](#working-with-openswitch)

## Overview
OpenSwitch requires a Linux-based OS in order to build an image or contribute to the source code. For documentation purposes only, a Windows machine can be used (refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup) guide for instructions).

## Setting up the environment

- If the [Quick Start Guide](quick-start) has already been followed, skip the Cloning OpenSwitch section and move to the [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system) section.
- Follow the instructions in [Setting up a Linux machine for OpenSwitch Development](linux-setup) to install all the required packages before proceeding with this guide.

## Cloning OpenSwitch
The OpenSwitch source code is accessible in the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects. If OpenSwitch is being built only for the purpose of creating a software image, then the [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project only needs to be cloned.


Refer to the [How to contribute to OpenSwitch](contribute-code) guide for instructions on how to contribute to the OpenSwitch code.

**Important Notes:**
* NFS and encryptfs file systems are not supported.
* Using directory paths containing whitespace or special characters is not supported.
* In proxied environments, the use of a DNS cache tool like `dnsmasq` is recommended.
* Once the cloned directory is configured it should not be moved.

To clone the OpenSwitch repository, use the following `git clone` command and URL. The use of `<directory>` is optional (if omitted, `<directory>` defaults to `ops-build`).

**Note:** This command clones the OpenSwitch development environment and build system, which can be used to build an OpenSwitch image. The command does not automatically clone all of the OpenSwitch source code, although the Development Environment commands (`make devenv_add`) can be used to access the rest of the source code. See the [Changing OpenSwitch Code](changing-openswitch-code) documentation for more details.

```
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
```

Once the repository is cloned, change the directory to the newly created repository.
```
$ cd <directory>
```

## Introduction to the OpenSwitch build system
The OpenSwitch build system is a GNU make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The make wrapper allows for easier common operations, and is used for typical image building and maintenance work. Advanced interfaces through the make wrapper allow access to the underlying facilities that Yocto provides. These advanced interfaces are not covered in this document.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:
```
$ make <TAB><TAB>
```

For example, `<TAB><TAB>` shows the targets that the make wrapper supports. A target to try first is "help":
```
$ make help
```

At this point, the help output indicates that the platform is not configured.

## Configuring an OpenSwitch product
After cloning the basic OpenSwitch repository, OpenSwitch needs to be configured for a specific platform. This must be done before most make targets are usable. In this example, OpenSwitch is configured to build for a `genericx86-64` platform:
```
$ make configure genericx86-64
```

## Building the product
Each platform defines the default output produced by the build. Typically, all that is required to invoke the build is `make`:
```
$ make
```

The build output can be found under the `images/` directory.

Please note that building as the root user is not supported.

## Deploying an image

The image that was built for the genericx86-64 platform in the earlier example can run inside a [Docker](http://docs.docker.com) instance as a simulated switch.

As a prerequisite, install the correct version of Docker.

### Install Docker on host machine
Since OpenSwitch is Ubuntu based, the default Docker installation does not work for OpenSwitch. The following briefly shows the steps needed to setup Docker for OpenSwitch:

Install Docker:

```
$ wget -qO- https://get.docker.com/ | sh
```
Add a user to the Docker group:
```
$ id
uid=1000(opsuser) gid=1000(opsuser) groups=1000(opsuser)
$ sudo usermod -aG docker opsuser
```

**Note**: Adding a user to the Docker group is mandatory. Once the user is added to the Docker group, log out and log in for the changes to take effect.

Complete documentation for docker on Ubuntu can be found  [here](https://docs.docker.com/installation/ubuntulinux)

### Simulated switch image
If an image for `genericx86-64` is built, a simulated switch image can be ran in [Docker](http://docs.docker.com/).

1. Make sure the Docker daemon is running. If it is not, execute the following:
```
sudo docker -d
```
2. Add yourself to the Docker group (this step is required only the first time Docker is installed):
```
sudo usermod -aG docker $USER
```
3. After building the image, execute the following command:
```
sudo make export_docker_image openswitch
```
4. Enter the following command to run the simulated switch image in Docker:
```
sudo docker run --privileged -v /tmp:/tmp -v /dev/log:/dev/log -v /sys/fs/cgroup:/sys/fs/cgroup -h ops --name ops openswitch /sbin/init &
```
5. Once the Docker switch is running, connect to it with the following command:
```
sudo docker exec -ti ops bash
```
Or connect using ssh with the switch IP given by Docker:
```
ssh admin@<Switch-IP>
```

To get the IP from the simulated switch:

1. Run `docker ps` to get the simulated switch ID (CONTAINER ID).
2. Run `docker inspect <id> | grep IPAddress`.
3. The IP address attribute is displayed.

**For example:**
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3ff6184474b9        openswitch          "/sbin/init"        About an hour ago   Up About an hour                        ops
$ dock inspect 3ff6184474b9 | grep IPAddress

"IPAddress": "172.17.0.1",

"SecondaryIPAddresses": null,
```

### Mininet based testing
OpenSwitch uses [Mininet](http://mininet.org/) to create custom topologies for testing. The minimum version required is 2.2.

```
$ git clone http://github.com/mininet/mininet
$ cd mininet
$ sudo python setup.py install
```

## Working with OpenSwitch
OpenSwitch provides a set of `make` targets to simplify working with Yocto:

| Command | Parameters | Description              |
|---------|------------|--------------------------|
|kernel   | None       | Builds the Linux kernel.  |
|fs       | None       | Builds the file system image (only supported in platforms that use a filesystem de-coupled from the kernel). |
|sdk      | None       | Builds the platform's SDK installer. |
|bake     | RECIPE     | Invoke bitbake over the recipe defined by the RECIPE environment variable. |
|devshell | RECIPE     | Invoke bitbake's devshell over the recipe defined by the RECIPE environment variable. |
|cleansstate | RECIPE  | Clears Yocto's shared state of the recipe defined by the RECIPE environment variable. |
|clean    | None       | Cleans all of the build, except the shared states, without un-configuring the directory. |
|distclean|None        | Cleans the build and unconfigures the project. |

Environment variables can be passed on the make invocation. For example, to clean the kernel build shared state:

```
$ make cleansstate RECIPE=virtual/kernel
```

**Hint**: In Yocto, the 'virtual' recipes are aliases to whatever version of the package is selected for the current platform.

Multiple arguments can also be passed to `bitbake` through the RECIPE environment variable. Following is an example to invoke `bitbake -c clean libyaml` through the `make` command:

```
$ make bake RECIPE="-c clean libyaml"
```

For information on developing for OpenSwitch, see the [How to contribute to OpenSwitch](contribute-code) and the [Changing OpenSwitch Code](changing-openswitch-code) documentation.
