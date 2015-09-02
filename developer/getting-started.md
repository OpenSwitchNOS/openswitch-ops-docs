---
title: Getting Started with OpenSwitch
layout: document
---

# Getting Started with OpenSwitch
OpenSwitch requires a Linux based OS in order to build an image or contribute to the source code. For documentation purposes only, a Windows box can be used (refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup.html) guide for instructions).

## Contents
- [Build System Requirements](#build-system-requirements)
- [Cloning OpenSwitch](#cloning-openswitch)
- [Build System](#the-openswitch-build-system)
- [Configuring an OpenSwitch Product](#configuring-an-openswitch-product)
- [Building the Product](#building-the-product)
- [Working with OpenSwitch](#working-with-openswitch)
- [Deploying an image](#deploying-an-image)

## Build System Requirements
* A GNU/Linux machine running a recent Linux distribution. OpenSwitch is developed in Ubuntu but Debian, Fedora or SuSe should work fine as well.
* At least 30GB of free disk space.

Two options exist to work with OpenSwitch:

* Bring Your Own Linux (BYOL): To develop using your own GNU/Linux machine, follow the instructions to [Setup a GNU/Linux machine for OpenSwitch development](./linux-setup.html).
* Use a Vagrant image: Follow this guide to [Use a Vagrant box for OpenSwitch development](./vagrant-setup.html).

**Proceed to the following sections once you have setup your own Linux system or a Vagrant box.**

## Cloning OpenSwitch
The OpenSwitch source code is accessible at the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects.  If you plan to build OpenSwitch to create a software image, only the overarching [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project needs to be cloned.

**Important Notes:**
* NFS and encryptfs file-systems are known to have problems working with the build system.
* Using directory paths containing whitespace or special characters is not supported.
* Once the directory is configured it should not be moved.
* In many proxied environments the DNS lookups performed to fetch `shared states` used by the build system could become a bottle neck. It is advisable to install a local DNS cache tool like `dnsmasq`.

To clone the OpenSwitch repository, use the following `git clone` command and URL. The use of `<directory>` is optional but recommended. Use the directory name as an indicator of the purpose of the OpenSwitch workspace.  If omitted, `<directory>` will default to `ops-build`.
```bash
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
```

Once the repo has been cloned, change directory to the newly created repository, `<directory>` will be `ops-build` if another name wasn't specified.
```bash
$ cd <directory>
```

## The OpenSwitch Build System
The OpenSwitch build system is a GNU Make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The wrapper makes doing common operations easier, and is what a developer would interact with for typical image building and maintenance work.  Advanced interfaces through the make wrapper allow access to the underlying facilities that Yocto provides. These advanced features are not covered here.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:
```bash
$ make <TAB><TAB>
```

For example, `<TAB>TAB>`will show the targets that the make wrapper supports. An obvious target to try first is "help", and the syntax is:
```bash
$ make help
```

At this point, the help output will indicate that the platform isn't configured.

## Configuring an OpenSwitch Product
After you clone the basic OpenSwitch repository, you can now proceed to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for an as5712 (Edge-Core AS5712) platform.
```bash
$ make configure as5712
```

## Building the Product
Each platform defines the default outputs produced by the build, typically all that is required is to invoke `make`:
```bash
$ make
```

All the build output will be found under the `images/` directory.

## Working with OpenSwitch
OpenSwitch provides a set of `make` targets to simplify working with Yocto:

| Command | Parameters | Description              |
|---------|------------|--------------------------|
|kernel   | None       | Builds the Linux kernel  |
|fs       | None       | Builds the file system image (only supported in platforms that use a filesystem de-coupled from the kernel) |
|sdk      | None       | Builds the platform's SDK installer |
|bake     | RECIPE     | Invoke bitbake over the recipe defined by the RECIPE environment variable |
|devshell | RECIPE     | Invoke bitbake's devshell over the recipe defined by the RECIPE environment variable |
|cleansstate | RECIPE  | Clears Yocto's shared state of the recipe defined by the RECIPE environment variable |
|clean    | None       | Cleans all the build (but not the shared states), without un-configuring the directory |
|distclean|None        | Cleans the build and unconfigures the project |

You can pass the environment variables on the make invocation, for example to clean the kernel build shared state:

```bash
$ make cleansstate RECIPE=virtual/kernel
```

**Hint**: In Yocto, the 'virtual' recipes are alias to whatever version of the package was selected for the current platform.

For information on developing for OpenSwitch, see the [How to contribute to the OpenSwitch Project Code](./contrib-code.html) and the [Development Environment](./dev-env.html) documentation.

## Deploying an image

You can uploaded your image to an Edge-Code AS5712 as described in [Physical Switch Image](#physical-switch-image), or run a [Simulated Switch Image](#simulated-switch-Image) in [Docker](http://docs.docker.com/) if you built a ```genericx86-64``` image.

### Physical Switch Image

After your build completes you will have an ONIE file that can be uploaded to the switch. The file can be found in the images folder and is typically called ```onie-installer-x86_64-as5712_54x```. For an as5712 platform, follow the instructions below.

1. Copy the ONIE file to a NFS shared directory.
2. Reboot the switch by typing ```reboot```.
3. When switch boots it will bring up a GNU GRUB selection screen.  Using your arrow keys move down to **ONIE** then select **ONIE : Rescue**.
4. Type the following command:
```
onie-nos-install tftp://<tftp-server>/<onie-file>
```
5. The switch will automatically reboot when done.


### Simulated Switch Image
If you build an image for ```genericx86-64```, it's possible to run a simulated switch image in [Docker](http://docs.docker.com/).

1. Make sure the Docker daemon is up and running. If it's not, run the following:
```
sudo docker -d
```
2. Add yourself to the docker group (this step is required only the first time):
```
sudo usermod -aG docker $USER
```
3. After building the image, run the following:
```
sudo make export_docker_image openswitch
```
4. To run the simulated switch image in Docker, type the following command:
```
sudo docker run --privileged -v /tmp:/tmp -v /dev/log:/dev/log -v /sys/fs/cgroup:/sys/fs/cgroup -h osw --name osw openswitch /sbin/init &
```
5. Once the Docker switch is running, you can connect to it via:
```
sudo docker exec -ti osw bash
```
