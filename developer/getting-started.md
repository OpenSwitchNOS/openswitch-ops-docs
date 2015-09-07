# Getting Started with OpenSwitch
OpenSwitch requires a Linux-based OS in order to build an image or contribute to the source code. For documentation purposes only, a Windows machine can be used (refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup.html) guide for instructions).

## Contents
- [Build system requirements](#build-system-requirements)
- [Cloning OpenSwitch](#cloning-openswitch)
- [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system)
- [Configuring an OpenSwitch product](#configuring-an-openswitch-product)
- [Building the product](#building-the-product)
- [Working with OpenSwitch](#working-with-openswitch)
- [Deploying an image](#deploying-an-image)

## Build System Requirements
* A GNU/Linux machine running a recent Linux distribution. OpenSwitch is developed in Ubuntu but Debian, Fedora or SuSe should work fine as well.
* At least 30 GB of free disk space.

Two options are available to work with OpenSwitch:

* Bring Your Own Linux (BYOL)--Follow the instructions to [Setup a GNU/Linux machine for OpenSwitch development](./linux-setup.html) to develop using your own GNU/Linux machine.
* Use a Vagrant image--See [Use a Vagrant box for OpenSwitch development](./vagrant-setup.html) to set up a Vagrant image.

**Proceed to the following sections once you have set up your own Linux system or a Vagrant box.**

## Cloning OpenSwitch
The OpenSwitch source code is accessible in the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects.  If you plan to build OpenSwitch to create a software image, only the overarching [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project needs to be cloned.

**Important Notes:**
* NFS and encryptfs file-systems have known issues with the build system.
* Using directory paths containing whitespace or special characters is not supported.
* In proxied environments is recommended the use of a DNS cache tool like `dnsmasq`.
* Once the directory is configured it should not be moved.

To clone the OpenSwitch repository, use the following `git clone` command and URL. The use of `<directory>` is optional (if omitted, `<directory>` defaults to `ops-build`).
```bash
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
```

Once the repository is cloned, change the directory to the newly created repository.
```bash
$ cd <directory>
```

## Introduction to the OpenSwitch build system
The OpenSwitch build system is a GNU make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The make wrapper allows for easier common operations, and it is what a developer uses for typical image building and maintenance work.  Advanced interfaces through the make wrapper allows access to the underlying facilities that Yocto provides. These advanced features are not covered here.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:
```bash
$ make <TAB><TAB>
```

For example, `<TAB><TAB>` shows the targets that the make wrapper supports. An obvious target to try first is "help", and the syntax is:
```bash
$ make help
```

At this point, the help output indicates that the platform isn't configured.

## Configuring an OpenSwitch product
After cloning the basic OpenSwitch repository, you need to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for an as5712 platform.
```bash
$ make configure as5712
```

## Building the product
Each platform defines the default outputs produced by the build. Typically, all that is required to invoke the build is `make`:
```bash
$ make
```

The build output is found under the `images/` directory.

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

You can pass the environment variables on the make invocation. For example, to clean the kernel build shared state:

```bash
$ make cleansstate RECIPE=virtual/kernel
```

**Hint**: In Yocto, the 'virtual' recipes are aliases to whatever version of the package is selected for the current platform.

For information on developing for OpenSwitch, see the [How to contribute to the OpenSwitch Project Code](./contrib-code.html) and the [Development Environment](./dev-env.html) documentation.

## Deploying an image

You can upload your image to an Edge-Code AS5712 as described in [Physical Switch Image](#physical-switch-image), or run a [Simulated Switch Image](#simulated-switch-Image) in [Docker](http://docs.docker.com/) if you built a ``genericx86-64` image.

### Physical switch image

After your build completes you will have an ONIE file that can be uploaded to the switch. The file can be found in the `images` directory and is typically called `onie-installer-x86_64-as5712_54x`. For an as5712 platform, follow these instructions:

1. Copy the ONIE file to your TFTP directory.
2. Reboot the switch.
3. When switch boots it will bring up a GNU GRUB selection screen.  Using your arrow keys move down to **ONIE** then select **ONIE : Install OS**.
4. Type the following commands:
```
tftp -g -r <onie-file> -l <onie-file> <tftp-server>
chmod +x <onie-file>
./<onie-file>
reboot
```

### Simulated switch image
If you build an image for```genericx86-64```, you can run a simulated switch image in [Docker](http://docs.docker.com/).

1. Make sure the Docker daemon is running. If it is not, execute the following:
```
sudo docker -d
```
2. Add yourself to the docker group (this step is required only the first time):
```
sudo usermod -aG docker $USER
```
3. After building the image, execute the following command:
```
sudo make export_docker_image openswitch
```
4. Enter the following command to run the simulated switch image in Docker:
```
sudo docker run --privileged -v /tmp:/tmp -v /dev/log:/dev/log -v /sys/fs/cgroup:/sys/fs/cgroup -h osw --name osw openswitch /sbin/init &
```
5. Once the Docker switch is running, you can connect to it with the following command:
```
sudo docker exec -ti osw bash
```
