# Quick Start Guide
The following guide is the minimal set of steps that you need to follow in order to build an OpenSwitch image. This guide assumes Ubuntu 14.04. See the [Getting Started Guide](./getting-started.html) for details about other platforms.

## Contents
- [System requirements](#system-requirements)
- [Define proxies](#define-proxies)
- [Install required packages](#install-required-packages)
- [Clone OpenSwitch](#clone-openswitch)
- [The OpenSwitch Build System](#the-openswitch-build-system)
- [Configure an OpenSwitch platform](#configure-an-openswitch-platform)
- [Build an image](#build-an-image)
- [More information](#more-information)

## System requirements
* Ubuntu 14.04.
* 30GB of free disk space available.

## Define proxies
If required, set up the HTTP(s) proxies.

```bash
$ export http_proxy=http://<proxy-url>:<proxy-port>
$ export https_proxy=https://<proxy-url>:<proxy-port>
```

## Install required packages

```bash
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib  build-essential chrpath screen curl device-tree-compiler libsdl1.2-dev xterm
```
If you encounter an error about `libsdl1.2`, the following can be used instead.

```bash
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib  build-essential chrpath screen curl device-tree-compiler xterm aptitude
$ sudo aptitude install libsdl1.2-dev
```

**DNS in proxied environments:**
In proxied environments the DNS lookups to fetch `shared states` used by the build system could become a bottle neck. Install a local DNS cache tool like `dnsmasq`.

```bash
$ sudo -E apt-get install dnsmasq
```

## Clone OpenSwitch
The source code is at the [OpenSwitch Git Repository](https://git.openswitch.net/), organized into several projects.  To build OpenSwitch only the [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project needs to be cloned.

```bash
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
```

## The OpenSwitch Build System
The OpenSwitch build system is a GNU Make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The wrapper makes doing common operations easier, and is what a developer would interact with for typical image building and maintenance work.

The wrapper supports bash shell tab completion, if it has been enabled in the shell.
````bash
$ make <TAB><TAB>
````

## Configure an OpenSwitch platform
It is necessary to configure OpenSwitch for an specific platform before other `make` targets are usable. The example below configures it to build for an as5712 (Edge-Core AS5712) platform. Use `make help` to list all available platforms.
````bash
$ make configure as5712
````

## Build an image
Each platform defines the default outputs produced by the build, typically all that is required is to invoke `make`.
````bash
$ make
````

All the build output will be found under the `images/` directory.

## More information
For more information on developing for OpenSwitch, see the [Getting Started Guide](./getting-started.html), [How to contribute to the OpenSwitch Project Code](./contrib-code.html) and [Development Environment](./dev-env.html) documentation.
