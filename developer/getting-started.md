---
title: Getting Started
layout: document
---
# Getting Started with OpenSwitch

## Build System Requirements

* A GNU/Linux machine running a recent distribution.
  * Unless you have powerful VMs, prefer real hardware.
  * OpenSwitch is developed on Ubuntu, but Debian, Fedora, SuSe should work fine as well.
* At least 30GB of disk available for each OpenSwitch build directory (usually takes way less, but this is the worst-case scenario).

If you are developing on your own GNU/Linux machine/VM, follow the instructions about how to [setup a GNU/Linux machine for OpenSwitch development](../linux-setup).

As an alternative, you can follow this guide on how to [use a Vagrant box for OpenSwitch development](../vagrant-setup).

## Accessing the OpenSwitch Source Code

The OpenSwitch source code is accessible at the [OpenSwitch git repository](https://git.openhalon.io/) website, where the source is organized into several projects.  To build OpenSwitch, only the over-arching [openhalon/openhalon](https://git.openhalon.io/cgit/openhalon/openhalon) project needs to be cloned, and the URLs used for cloning are listed at the bottom of the page.

**Note**: If the build system is behind a firewall, using a proxy may be required to access the git repository, as well as during the build process. The correct syntax of `https_proxy` should include the complete URL including the protocol (`https://`) and port, for example:

````bash
$ export https_proxy=https://myproxy.com:8080
````

Also, in many proxied environments the DNS lookups performed to fetch shared states used by the build system could be a bottle neck and slow build time. It's advisable that you install a local DNS cache like dnsmasq or unbound if possible. For example to install dnsmasq in Ubuntu use:

````bash
$ sudo -E apt-get install dnsmasq
````

## Cloning OpenSwitch
Make sure you are using `/ws/<user name>/` as your development directory.
If you are using your local machine, create the folder inside your user `/home/<user name>/`

Once any proxy issues have been resolved, cloning the OpenSwitch code is possible:

````bash
$ git clone https://git.openhalon.io/openhalon/openhalon [<directory>]
````

If you are trying to clone and geting this error:

````bash
Cloning into 'openhalon'...
fatal: unable to access 'https://git.openhalon.io/openhalon/openhalon/': server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
````

Enter this line as a temporary solution for now.
````bash
$ git config --global http."https://git.openhalon.io/".sslVerify false
````

The use of `<directory>`, although optional, is recommended to be used as an indicator of the purpose of the OpenSwitch workspace.  If omitted, `<directory>` will default to `openhalon`.

````bash
$ cd <directory>
````
to work with this instance of OpenSwitch.

**NOTES**:
* Using directory paths containing whitespace or other special characters (ones requiring escaping in the shell) is not supported.
* Once the directory is configured it should not be moved.

## The OpenSwitch Build System
The OpenSwitch build system is a GNU Make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The wrapper makes doing common operations easier, and is what a developer would interact with for typical image building and maintenance work.  Advanced interfaces through the make wrapper allow access to the underlying facilities that Yocto provides. These advanced features are not covered here.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:

````bash
$ make <TAB><TAB>
````

will show the targets that the make wrapper supports. An obvious target to try first is "help", and the syntax is (of course):

````bash
$ make help
````

At this point, the help output indicates that the platform isn't configured.

## Configuring a OpenSwitch Product
After you clone the basic OpenSwitch repository, you can now proceed to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for an as5712 (Edge-Core AS5712) platform.

````bash
$ make configure as5712
````

## Building the Product
Each platform defines the default outputs produced by the build, typically all that is required is to invoke make:

````bash
$ make
````

All the build output will be found under the <tt>images/</tt> directory.

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

````bash
$ make cleansstate RECIPE=virtual/kernel
````

**HINT**: In yocto, the 'virtual' recipes are alias to whatever version of the package was selected for the current platform.

For information on developing OpenSwitch software, see the documentation on the [Development Environment](development- environment).
