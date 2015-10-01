# Step-by-Step Guide
OpenSwitch requires a Linux-based OS in order to build an image or contribute to the source code. For documentation purposes only, a Windows machine can be used (refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup) guide for instructions).

## Contents
- [Setting up the environment](#setting-up-the-environment)
- [Cloning OpenSwitch](#cloning-openswitch)
- [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system)
- [Configuring an OpenSwitch product](#configuring-an-openswitch-product)
- [Building the product](#building-the-product)
- [Deploying an image](#deploying-an-image)
	- [Install docker on host machine](#install-docker-on-host-machine)
	- [Simulated switch image](#simulated-switch-image)
	- [Mininet based testing](#mininet-based-testing)
- [Working with OpenSwitch](#working-with-openswitch)

## Setting up the environment

- If you already followed the [Quick Start Guide](quick-start), you can skip the Cloning OpenSwitch section and move to [Introduction to the OpenSwitch build system](#introduction-to-the-openswitch-build-system) section
- Follow the instructions in [Setting up a Linux machine for OpenSwitch Development](linux-setup) to install all the required packages before proceeding with this guide

## Cloning OpenSwitch
The OpenSwitch source code is accessible in the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects.  If you only plan to build OpenSwitch for the purpose of creating a software image, then you only need to clone the  [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project.

Refer to the [How to contribute to OpenSwitch](contribute-code) guide for instructions on how to contribute to the OpenSwitch code.

**Important Notes:**
* NFS and encryptfs file systems are not supported.
* Using directory paths containing whitespace or special characters is not supported.
* In proxied environments the use of a DNS cache tool like `dnsmasq` is recommended.
* Once the cloned directory is configured it should not be moved.

To clone the OpenSwitch repository, use the following `git clone` command and URL. The use of `<directory>` is optional (if omitted, `<directory>` defaults to `ops-build`).
```
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
```

Once the repository is cloned, change the directory to the newly created repository.
```
$ cd <directory>
```

**Note:** This command clones the OpenSwitch development environment and build system which you can use to build an OpenSwitch image. The command does not automatically clone all of the OpenSwitch source code, although you can use the Development Environment commands (`make devenv_add`) to access the rest of the source code. See the [Changing OpenSwitch Code](changing-openswitch-code) documentation for more details.

## Introduction to the OpenSwitch build system
The OpenSwitch build system is a GNU make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The make wrapper allows for easier common operations, and it is what a developer uses for typical image building and maintenance work.  Advanced interfaces through the make wrapper allows access to the underlying facilities that Yocto provides. These advanced features are not covered here.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:
```
$ make <TAB><TAB>
```

For example, `<TAB><TAB>` shows the targets that the make wrapper supports. An obvious target to try first is "help", and the syntax is:
```
$ make help
```

At this point, the help output indicates that the platform isn't configured.

## Configuring an OpenSwitch product
After cloning the basic OpenSwitch repository, you need to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for a `genericx86-64` platform.
```
$ make configure genericx86-64
```

## Building the product
Each platform defines the default output produced by the build. Typically, all that is required to invoke the build is `make`:
```
$ make
```

The build output can be found under the `images/` directory.


## Deploying an image

The image that was built for the genericx86-64 platform in the earlier step can run inside a [Docker](http://docs.docker.com) instance as a simulated switch.
As a prerequisite, install the correct version of docker.

### Install docker on host machine
Since OpenSwitch is Ubuntu based, the default docker installation does not work for OpenSwitch. The steps below briefly mention the steps needed to setup docker for OpenSwitch

Install docker:

```
$ wget -qO- https://get.docker.com/ | sh
```
Add user to the docker group
```
$ id
uid=1000(opsuser) gid=1000(opsuser) groups=1000(opsuser)
$ sudo usermod -aG docker opsuser
```

**Note**: Adding the user to the docker group is mandatory. Once the user is added to the docker group, logout and login for the changes to take effect.

Complete documentation for docker on Ubuntu can be found at https://docs.docker.com/installation/ubuntulinux/

### Simulated switch image
If you build an image for `genericx86-64`, you can run a simulated switch image in [Docker](http://docs.docker.com/).

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
sudo docker run --privileged -v /tmp:/tmp -v /dev/log:/dev/log -v /sys/fs/cgroup:/sys/fs/cgroup -h ops --name ops openswitch /sbin/init &
```
5. Once the Docker switch is running, you can connect to it with the following command:
```
sudo docker exec -ti ops bash
```
Or you can connect using ssh using the switch IP given by docker.
```
ssh admin@<Switch-IP>
```

To get the IP from the simulated switch the following can be done:

1. Run `docker ps` to get the simulated switch ID (CONTAINER ID)
2. Run `docker inspect <id> | grep IPAddress`
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
|kernel   | None       | Builds the Linux kernel  |
|fs       | None       | Builds the file system image (only supported in platforms that use a filesystem de-coupled from the kernel) |
|sdk      | None       | Builds the platform's SDK installer |
|bake     | RECIPE     | Invoke bitbake over the recipe defined by the RECIPE environment variable |
|devshell | RECIPE     | Invoke bitbake's devshell over the recipe defined by the RECIPE environment variable |
|cleansstate | RECIPE  | Clears Yocto's shared state of the recipe defined by the RECIPE environment variable |
|clean    | None       | Cleans all the build (but not the shared states), without un-configuring the directory |
|distclean|None        | Cleans the build and unconfigures the project |

You can pass the environment variables on the make invocation. For example, to clean the kernel build shared state:

```
$ make cleansstate RECIPE=virtual/kernel
```

**Hint**: In Yocto, the 'virtual' recipes are aliases to whatever version of the package is selected for the current platform.

For information on developing for OpenSwitch, see the [How to contribute to OpenSwitch](contribute-code) and the [Changing OpenSwitch Code](changing-openswitch-code) documentation.
