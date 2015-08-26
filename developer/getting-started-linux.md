# Getting Started with OpenSwitch
OpenSwitch requires a Linux based OS in order to build an image or contribute to the source code. For documentation purposes only,a Windows box can be used too. Refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup.html) guide for instructions if using Windows. Or follow the instructions below if you are a Linux user.

OpenSwitch runs [Gerrit](https://code.google.com/p/gerrit/) for online code reviews and [Git](http://git-scm.com/) as the distributed version control system.
The [OpenSwitch Review](https://review.openswitch.net/) site is the entry point for change, review, test and inclusion in one of the OpenSwitch projects.

## Contents
- [Build System Requirements](#build-system-requirements)
- [Cloning OpenSwitch](#cloning-openswitch)
- [Setup the SSH key to work with the Review Server](#setup-the-ssh-key-to-work-with-the-review-server)
	- [Create the SSH keys](#create-the-ssh-keys)
	- [Add the SSH Public Key to the OpenSwitch Gerrit Review site](#add-the-ssh-public-key-to-the-openswitch-gerrit-review-site)
	- [Verify that an username is assigned to the Gerrit Review account](#verify-that-an-username-is-assigned-to-the-gerrit-review-account)
	- [Client SSH configuration](#client-ssh-configuration)
- [Working behind a firewall](#working-behind-a-firewall)
- [Configuring Git and Gerrit](#configuring-git-and-gerrit)
- [Installing git-review to work with the Review System](#installing-git-review-to-work-with-the-review-system)
- [The OpenSwitch Build System](#the-openswitch-build-system)
- [Configuring an OpenSwitch Product](#configuring-an-openswitch-product)
- [Building the Product](#building-the-product)
- [Working with OpenSwitch](#working-with-openswitch)

## Build System Requirements
* A GNU/Linux machine running a recent Linux distribution.
  * Unless you have powerful VMs, prefer real hardware.
  * OpenSwitch is developed in Ubuntu but Debian, Fedora, SuSe should work fine as well.
* At least 30GB of free disk space available for each OpenSwitch build directory (usually takes less, but this is the worst-case scenario).

Two options exist to work with OpenSwitch:

* BYOL (Bring Your Own Linux). Develop using your own GNU/Linux machine/VM.
 * Follow the instructions to [Setup a GNU/Linux machine for OpenSwitch development](./linux-setup.html).
* Use a Vagrant image.
 * Follow this guide to [Use a Vagrant box for OpenSwitch development](./vagrant-setup.html).

**Proceed to the following sections once you have setup your own Linux system or a Vagrant box.**

## Cloning OpenSwitch
The OpenSwitch source code is accessible at the [OpenSwitch Git Repository](https://git.openswitch.net/), where the source code is organized into several projects.  If you plan to build OpenSwitch to create a software image, only the overarching [openswitch/ops-build](https://git.openswitch.net/cgit/openswitch/ops-build) project needs to be cloned.

The following file-systems are known to have problems working with the build system:
* encryptfs
* NFS

**About DNS in proxied environments**:
In many proxied environments the DNS lookups performed to fetch `shared states` used by the build system could become a bottle neck. It is advisable to install a local DNS cache tool like `dnsmasq`.

**Important Notes:**
* Using directory paths containing whitespace or other special characters (ones requiring escaping in the shell) is not supported.
* Once the directory is configured it should not be moved.

To clone the OpenHalon repository, use the following `git clone` command and URL. The use of `<directory>` is optional but recommended. Use the directory name as an indicator of the purpose of the OpenSwitch workspace.  If omitted, `<directory>` will default to `ops-build`.
````bash
$ git clone https://git.openswitch.net/openswitch/ops-build [<directory>]
````

Once the repo has been cloned, change directory to the newly created repository, `<directory>` will be `ops-build` if another name wasn't specified.
````bash
$ cd <directory>
````

## Setup the SSH key to work with the Review Server
In order to use the development environment you need to setup an SSH key with the Gerrit Review server at [review.openswitch.net](https://review.openswitch.net).

### Create the SSH keys
Do this only if no SSH keys have been created earlier for this development machine. To find out if keys exist for this development machine check the contents of the `~/.ssh/` directory. The following two files will exist if keys were already created:
* `id_rsa`
* `id_rsa.pub`

If the `~/.ssh` directory or the files do not exist, then proceed to generate your SSH keys with the commands below:
```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa
```

You will be prompted for a location to save the keys, and a pass-phrase for the keys. Accept the default location to save the keys and provide a pass-phrase. The pass-phrase is important as it keeps your keys secure while stored in your hard-drive. Do remember the pass-phrase as it will be needed to work with Gerrit. An example output is the following:
```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/b/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/b/.ssh/id_rsa.
Your public key has been saved in /home/b/.ssh/id_rsa.pub.
```

The public key needed to work with Gerrit is saved at `~/.ssh/id_rsa.pub`. The contents of this file (this is a text file) will be needed in the next step.

If you get the `Agent admitted failure to sign using the key.` error message as seen below, simply run the command `ssh-add` after the error message.

```bash
"Agent admitted failure to sign using the key."
# debug1: No more authentication methods to try.
# Permission denied (publickey).
```

### Add the SSH Public Key to the OpenSwitch Gerrit Review site
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
 * OpenSwitch uses a `GitHub` account to login to the Review Site (Gerrit).
 * If you do not have an account create one by cliking on `Sign up` in the GitHub page after you try to login to the [OpenSwitch Review](https://review.openswitch.net/).
 * Keep record of the password and username as this is your Gerrit user and is needed to work with OpenSwitch.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Select `SSH Public Keys` in the panel on the left.
* Paste the SSH public key (`id_rsa.pub` contents) in the input area for use with this site. The instructions for generating an SSH key are accessible just above the input area.
* Click `Add`

### Verify that an username is assigned to the Gerrit Review account
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Click on the `Profile` link (left side)
* Verify that an username exists, or define one if it does not exist.
* Keep track of the username as this is the Gerrit User and will be used to work with OpenSwitch

### Client SSH configuration
* Add an entry similar to this to your `~/.ssh/config` file (preserve the indentation), which directs SSH to the proper SSH private key file to use for the OpenSwitch review website:
```bash
Host review.openswitch.net
  User <gerrit-user>
  IdentityFile ~/.ssh/id_rsa
```

## Working behind a firewall
The network port used by Gerrit (29418) on the review website may be blocked in some networks, for example on a corporate network. One workaround (besides asking your ISP or corporate IT team to open the port) is to tunnel using your HTTP proxy. You can achieve that with the `socat` or `nc` command line utilities, or any other of your preference. Follow the instructions below to use `socat` or `nc` to configure a proxy for the SSH traffic.

Take note of the proxy information.
* Proxy URL (e.g web-proxy.location.domain.com). Referred as `<proxy-url>` below.
* Proxy Port (e.g 8080). Referred as `<proxy-port>` below.

Using `socat`
* Install `socat` if not yet installed
* Open (or create) the `~/.ssh/config` file and add the following lines (preserve the indentation):
```
Host review.openswitch.net
   ProxyCommand socat - PROXY:<proxy>:%h:%p,proxyport=<proxy-port>
```

Using `nc` (**netcat**)
* Install `nc` if not yet installed
* Open (or create) the `~/.ssh/config` file and add the following lines (preserve the indentation):
```
Host review.openswitch.net
   ProxyCommand nc -x <proxy-url>:<proxy-port> -X connect %h %p
```
For more details and examples for adding user/passwords, see [this page](http://gitolite.com/git-over-proxy.html).

## Configuring Git and Gerrit
You need to configure your local repository to use your Gerrit account.
* Set `gitreview.username` with your Gerrit login.
```bash
$ git config --global gitreview.username <gerrit-user>
```
* Configure email and name in Git:
```bash
$ git config --global user.email <email>
$ git config --global user.name <name>
```

If your username for Gerrit is different from the local machine login, export the `REVIEWUSER` variable. Preferable in some place where it gets always setup, like `~/.bashrc`. (*gitreview.username* will be used in preference to the *REVIEWUSER* environment variable if both are set).
```bash
$ export REVIEWUSER=<gerrit-user>
```

## Installing git-review to work with the Review System
`git-review` is used to contribute changes back to the OpenSwitch project.

Install `git-review` as described [here](http://www.mediawiki.org/wiki/Gerrit/git-review), if not already installed on the development machine.

* Setup git-review
```bash
$ git-review -s
```

## The OpenSwitch Build System
The OpenSwitch build system is a GNU Make-based wrapper around the [Yocto Project](https://www.yoctoproject.org). The wrapper makes doing common operations easier, and is what a developer would interact with for typical image building and maintenance work.  Advanced interfaces through the make wrapper allow access to the underlying facilities that Yocto provides. These advanced features are not covered here.

The wrapper supports bash shell tab completion, if it has been enabled in the shell:
````bash
$ make <TAB><TAB>
````

For example, `<TAB>TAB>`will show the targets that the make wrapper supports. An obvious target to try first is "help", and the syntax is:
````bash
$ make help
````

At this point, the help output will indicate that the platform isn't configured.

## Configuring an OpenSwitch Product
After you clone the basic OpenSwitch repository, you can now proceed to configure OpenSwitch for a specific platform. This must be done before most other make targets are usable. In this example, OpenSwitch is configured to build for an as5712 (Edge-Core AS5712) platform.
````bash
$ make configure as5712
````

## Building the Product
Each platform defines the default outputs produced by the build, typically all that is required is to invoke `make`:
````bash
$ make
````

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

````bash
$ make cleansstate RECIPE=virtual/kernel
````

**Hint**: In Yocto, the 'virtual' recipes are alias to whatever version of the package was selected for the current platform.

For information on developing for OpenSwitch, see the [How to contribute to the OpenSwitch Project Code](./contrib-code.html) and the [Development Environment](./dev-env.html) documentation.
