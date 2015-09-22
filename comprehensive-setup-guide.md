# Comprehensive Setup Guide
OpenSwitch requires a Linux-based OS in order to build an image or contribute to the source code. For documentation purposes only, a Windows machine can be used (refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup) guide for instructions).

## Contents
- [Cloning OpenSwitch](#cloning-openswitch)
- [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system)
- [Configuring an OpenSwitch product](#configuring-an-openswitch-product)
- [Building the product](#building-the-product)
- [Working with OpenSwitch](#working-with-openswitch)
- [Deploying an image](#deploying-an-image)


## Setting up the environment

- If you already followed the [Quick Start Guide](#quick-start), you can skip this step and move to [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system) section
- Follow the instructions in [Setting up a Linux machine for OpenSwitch Development](linux-setup) to install all the required packages before proceeding with this guide

## Cloning OpenSwitch
The OpenSwitch source code is accessible in the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects.  If you plan to build OpenSwitch to create a software image, only the overarching [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project needs to be cloned.

**Important Notes:**
* NFS and encryptfs file systems are not supported.
* Using directory paths containing whitespace or special characters is not supported.
* In proxied environments the use of a DNS cache tool like `dnsmasq` is recommended.
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
After cloning the basic OpenSwitch repository, you need to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for a `genericx86-64` platform.
```bash
$ make configure genericx86-64
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

For information on developing for OpenSwitch, see the [How to contribute to the OpenSwitch Project Code](./contrib-code) and the [Development Environment](./dev-env) documentation.

## Deploying an image

You can upload your image to a Simulated Switch in [Docker](http://docs.docker.com/) when you built a `genericx86-64` image.


### Simulated switch image
If you build an image for `genericx86-64`, you can run a simulated switch image in [Docker](http://docs.docker.com/).

1. Make sure the Docker daemon is running. If it is not, execute the following:
```bash
sudo docker -d
```
2. Add yourself to the docker group (this step is required only the first time):
```bash
sudo usermod -aG docker $USER
```
3. After building the image, execute the following command:
```bash
sudo make export_docker_image openswitch
```
4. Enter the following command to run the simulated switch image in Docker:
```bash
sudo docker run --privileged -v /tmp:/tmp -v /dev/log:/dev/log -v /sys/fs/cgroup:/sys/fs/cgroup -h ops --name ops openswitch /sbin/init &
```
5. Once the Docker switch is running, you can connect to it with the following command:
```bash
sudo docker exec -ti ops bash
```
Or you can connect using ssh using the switch IP given by docker.
```bash
ssh admin@<Switch-IP>
```
***Note**: You can get the IP from the simulated switch with `dock inspect`. Search for the simulated switch ID, then you can run `docker inspect [id]` and look for the IP address attribute.

