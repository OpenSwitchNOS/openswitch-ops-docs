# Develop on OpenSwitch

In addition to building images, the OpenSwitch build system assists in the development process by creating and maintaining an area containing a subset of the project's source files.  Once an OpenSwitch repository has been cloned and configured, working in the development environment may begin, i.e. no need to build the image.

The OpenSwitch development environment is built upon the `devtool` recently written by Paul Eggleton.

**All of the packages in OpenSwitch are not yet supported in the development environment.**

- [Managing the development environment](#managing-the-development-environment)
	- [Prerequisites for using the development environment](#prerequisites-for-using-the-development-environment)
    - [devenv_init](#devenv_init)
    - [devenv_list_all](#devenv_list_all)
    - [devenv_status](#devenv_status)
    - [devenv_add](#devenv_add)
    - [devenv_rm](#devenv_rm)
    - [devenv_update_recipe](#devenv_update_recipe)
    - [devenv_clean](#devenv_clean)
    - [devenv_cscope](#devenv_cscope)
    - [devenv_import](#devenv_import)
    - [devenv_patch_recipe](#devenv_patch_recipe)
    - [devenv_refresh](#devenv_refresh)
    - [git_pull](#git_pull)
- [Build system infrastructure](#build-system-infrastructure)
- [Working with modified packages](#working-with-modified-packages)
	- [Building and cleaning](#building-and-cleaning)
	- [Deploying a package](#deploying-a-package)
	- [Undeploying a package](#undeploying-a-package)
	- [Building the documentation](#building-the-documentation)
	- [Troubleshooting](#troubleshooting)
- [Working with branches](#working-with-branches)
- [Setting up an NFS root environment for development](#setting-up-an-nfs-root-environment-for-development)
	- [Introduction](#introduction)
	- [Requirements](#requirements)
	- [Using NFS root](#using-nfs-root)
		- [Configuring your NFS server IP address in the image](#configuring-your-nfs-server-ip-address-in-the-image)
		- [Directory stucture of the deployed NFS directory](#directory-stucture-of-the-deployed-nfs-directory)
		- [Deploying your NFS directory ](#deploying-your-nfs-directory)
		- [Working with devenv on NFS](#working-with-devenv-on-nfs)
		- [Booting your hardware with NFS](#booting-your-hardware-with-nfs)

## Managing the development environment

All targets for the development environment management begin with `devenv_` and can be shown with tab-completion:
```bash
$ make devenv_<TAB><TAB>
devenv_add            devenv_clean          devenv_import         devenv_init
devenv_patch_recipe   devenv_rm             devenv_status         devenv_update_recipe
```

Detailed information about each available target is provided below.

The area for developing software is under the `src/` directory at the top level of the workspace. Use `make devenv_add <package>` to fetch packages and its source code to `src/`. The subdirectories contain the source for the packages.

### Prerequisites for using the development environment
1. Follow the [Getting Started](getting-started) guide to configure your system.
2. Review the [How to contribute to the OpenSwitch Project Code](contribute-code) guide.
3. Follow the [OpenSwitch Coding Style](contribute-code#openswitch-coding-style) for new code, or follow the existing style in non-OpenSwitch modules.

### devenv_init
Initiates the development environment.
```bash
$ make devenv_init
```
If git-review has been installed, `make`-ing the `devenv_init` target will configure the workspace for possible future change submissions.

### devenv_list_all
This command shows the list of available packages for use with the platform that has been configured.
```bash
$ make devenv_list_all
Build System for openswitch

Platform: as5712

List of available devenv packages for as5712 platform:
  * ops-aaa-utils
  * ops-arpmgrd
  * ops-bufmond
  * ops-cfgd
  * ops-cli
  * ops-config-yaml
  * ops-dhcp-tftp
```

### devenv_status
The `devenv_status` target shows the status of the development environment.
```bash
$ make devenv_status
ops-vland: <path>/src/ops-vland
```

### devenv_add
Packages to be developed can be added to the collection, one or more at a time, by using this command, as shown in the following example. As the packages are added, each one will be fetched into the development `src/` directory in separate sub-directories. Each package is also unpacked and patched as required.
```bash
$ make devenv_init
...
$ make devenv_add ops-vland ops-pmd
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
ops-vland: <path>/ops-build/src/ops-vland
```

### devenv_rm
Complementary to the `devenv_add` target, the `devenv_rm` target is used to remove packages from the collection, as shown in the following example:
```bash
$ make devenv_rm ops-vland
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
```
**Note**: Unsaved changes to the source in the removed package are lost.

### devenv_update_recipe
`devenv_update_recipe` is used to update build recipes after changes have been committed to a package in the development environment.  The workflow is as follows:
```bash
$ make devenv_init
...
$ make devenv_add ops-vland
... # make changes to src/ops-vland/...
$ make ops-vland-build
...
$ make ops-vland-deploy TARGET="root@xx.xx.xx.xx"
... # test your changes ... they work!
$ git -C src/ops-vland add <the files you changed>
$ git -C src/ops-vland commit -sm "Fix something"
...
$ make devenv_update_recipe ops-vland
... # ops-vland recipe is updated with the changes committed to "Fix something"
```

#### devenv_clean
The complementary target to `devenv_init` is `devenv_clean`. `devenv_clean` removes any/all package sources, in addition to the removal of the `src/` directory.
**Note**: Unsaved changes to the source in any/all packages are lost.

#### devenv_cscope
`devenv_cscope` is a tool for browsing in a development enviroment. `devenv_cscope` has the same features as `cscope` for Linux. It is necessary to install the `cscope` tool for Linux before using `devenv_cscope`.
```bash
$  make devenv_cscope
```

#### devenv_import
`devenv_import` imports code. Specify the name of the package you want to import.
```bash
$  make devenv_import <package_name>
```

#### devenv_patch_recipe
If you commit a recipe that is not from OpenSwitch, you must run this command after the commit in order to create the necessary patch for the OpenSwitch environment.
```bash
$  make devenv_patch_recipe
```

#### devenv_refresh
This command performs a `git pull --rebase` in all your devenv repositories. If you have local changes not staged, the git pull will fail, and you are shown a warning in red to let you know that particular repository was not updated.
```bash
$  make devenv_refresh
```

#### git_pull

This command performs a `git pull --rebase` in the base Git repository and the overlay repositories. This command also works with the `devenv` environment, and all of its repositories, similar to `devenv_refresh`.
```bash
$  make git_pull
```
## Build system infrastructure

The build system is based in the [Yocto Project](https://www.yoctoproject.org/about). This document does not cover how Yocto works, but it does include some important aspects related to OpenSwitch.

The following directories and files are displayed after you clone OpenSwitch on your machine:

```bash
$ ls
build/  COPYING  images/  Makefile  README.md  src/  tools/  yocto/
```
**build/**: This directory contains user configuration files and the output generated by Open Embedded; it is standard configuration.

**COPYING**: Licensing information. For more details, see the [Apache License](http://www.apache.org/licenses/LICENSE-2)

**images/**: Symbolic links to the images that have been built. The most important file is `openswitch-disk-image-<platform>.tar.gz`

**Makefile**: The OpenSwitch Makefile.

**tools/**: This directory contains tools primarily used by the build system. This directory includes the `Rules.make` file, which is the core of `Makefile`.

**yocto/**: The core of the build system. Inside this directory:

```bash
$ ls
openswitch/  poky/
```

**poky/**: This directory is the core of the Yocto project. For more information, see [YoctoProject Documentation](http://www.yoctoproject.org/docs/1.6/dev-manual/dev-manual.html)

**openswitch/**: This directory is the core OpenSwitch directory. Its contents are shown in the following example:

```bash
$ ls
Rules.make  meta-distro-openswitch/  meta-platform-openswitch-as5712/
meta-foss-openswitch/    meta-platform-openswitch-genericx86-64/
```

**meta-foss-openswitch/**: This directory contains all the recipes and patch for FOSS (**Free and Open Source Software**)

**meta-distro-openswitch/**: This directory contains all the recipes and patches related to the distribution, including all recipes related with the core and the kernel of OpenSwitch.

**meta-platform-openhalon-XXX/**: This directory contains all the recipes and patch related to a specific platform. For example, `meta-platform-openswitch-genericx86-64` contains all the code Yocto requires to build a new image for the x86 platform.

Every directory contains a structure to keep its contents in order. For example, the  `meta-platform-openswitch-genericx86-64` directory contains subdirectories for core `recipes`,`kernel recipes`, in addition to other subdirectories. For example, if you have to change something in the kernel and the change is just for x86, open `yocto/openswitch/meta-platform-openswitch-genericx86-64/recipes-kernel` and add your recipe or the `bbapend` file.

## Working with modified packages
All packages in the workspace have targets to `build`, `clean`, `deploy` or `undeploy`, which are formed by prefixing the package name to the operation, as shown in the following example:
```bash
$ make devenv_init
...
$ make devenv_add ops-vland
...
$ make ops-vland-<TAB><TAB>
ops-vland-build     ops-vland-clean     ops-vland-deploy    ops-vland-undeploy
```

### Building and cleaning
A package can be built by using the target with the `-build` suffix.

**Note:** The `-clean` suffixed target for the package is intended to return the package to a clean state, but it is not functional at this time.

**Note:** The build system ensures that any prerequisites for this target are updated before the package build occurs.

### Deploying a package
By specifying a running target, accessible via SSH, the results of the built package can be installed using the `-deploy` target. The following example uses SSH as user root to install the results of build vland on a device at address 10.0.2.5:

```bash
$ make vland-deploy TARGET="root@10.0.2.5"
```

To display the deployment progress, add the `-s` option to `TARGET`, for example, `TARGET="-s root@10.0.2.5"`.
To do a dry-run of the deployment without actually deploying the content, use `-n`, e.g. `TARGET="-n root@10.0.2.5"`.

**Note:** Deployment of files fail if they are "busy" on the target.

### Undeploying a package
Undeploying a package removes the content from the target device.  It does not replace or restore the original content.  The syntax is similar to that used for deploying.

### Building the documentation
The OpenSwitch project uses the markdown markup language to generate its documentation. To build the OpenSwitch documentation, install `markdown`.

After `markdown` is installed, the documentation can be built by `make`-ing `dist-docs` as shown in the following example.
```bash
make devenv_init
make devenv_add ops-vland
cd src/ops-vland/build
make dist-docs
```

### Troubleshooting
While building the project, you might encounter disk space error messages, such as the following:
```bash
ERROR: No space left on device or exceeds fs.inotify.max_user_watches?
ERROR: To check max_user_watches: sysctl -n fs.inotify.max_user_watches.
ERROR: To modify max_user_watches: sysctl -n -w fs.inotify.max_user_watches=<value>.
```
The problem could be caused by not enough disk space available on your system. Make sure you have enough free disk space by using the `df -h` command, as shown in the following example:
```bash
$ df -h
Filesystem                                Size  Used Avail Use% Mounted on
/dev/mapper/lvm_vg-lvm_root_lv  457G  146G  288G  34% /
none                                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                                      7.8G  4.0K  7.8G   1% /dev
...omitted output
```

If enough disk space is available, modify the file `/etc/sysctl.d/30-tracker.conf`. The recommendation is to multiply the value of `fs.inotify.max_user_watches` by 2.


## Working with branches
Usually only owners of the respective git repo (project owners) or the members of certain privileged user groups can create new feature branches, and these branches are restricted to have the prefix `feature/` on it.

Assume you are creating a branch `foo` on the project `sysd`, you would use the following commands:

```bash
make devenv_add sysd
cd src/sysd
git review -s # Setup the Gerrit remote
git checkout -b feature/foo # The -b flag creates the new branch
git push gerrit feature/foo # Publishes the new branch
# Do changes needed
git commit -s # This step is needed for branch creation even if you do not have any code changes
git review feature/foo # Send the review for this branch
```

If you are a developer working on a feature branch (e.g `foo`) that already exists, your workflow would resemble the following example:

``` bash
make devenv_add sysd
cd src/sysd
git pull --rebase # update the local repo to find any new remote branches
git checkout -b feature/foo --track origin/feature/foo
git branch -u origin/feature/foo
# do your changes and commit
git review feature/foo # send the review for this branch
```

If you are a developer planning to merge your branch (source) to another branch (target):

```bash
make devenv_add sysd
cd src/sysd
git checkout <target>
git pull # update the local repo to find any new remote branches
git merge --no-ff <source> # merge the source branch changes to the target
git commit -s --amend # this will modify the commit message to include a ChangeId. Without the ChangeID, commits will not go through
git review <target> # send the review for target branch. The review for merge will not have any code as this is only a merge
```

## Setting up an NFS root environment for development
The OpenSwitch build environment supports working with an NFS Root Setup that speeds up development workflows. This section provides the instructions to set it up.

### Introduction
NFS root environment is a setup where your target hardware boots its root file system from a directory on the developer's machine shared over the NFS (Network File System). This setup is very convenient since it requires almost no effort to update the code/configuration on the target when developers are making changes.

### Prerequisites
A Linux host machine that has an NFS server installed.

### Using NFS root
The OpenSwitch build system includes logic that simplifies working with NFS root configuration.

### Configuring your NFS server IP address in the image
By default the build system creates a debug entry on the boot menus of the switch to start with an NFS root.
1. Configure the IP address and path to your NFS root for the menu by modifying the file `build/nfs.conf` on the build directory.

 For example, to enable automatic detection of the IP address from the main subnet used to reach the Internet on your host machine and the default file system deploy location, ensure the following is in the file:
```bash
echo 'NFS_SERVER_IP ??= "${@get_default_ip(d)}"' > build/nfs.conf
echo 'NFS_SERVER_PATH ??= "${BUILD_ROOT}/nfsroot-${MACHINE}"' >> build/nfs.conf
```
You can also specify the IP address directly.

2. Rebuild your image and re-flash it on the target hardware. As alternative you can manually modify the address on the bootloader before booting. <!--How would you do this? -->

### Directory structure of the deployed NFS directory
The build system includes a make target that deploys the root file system into a directory with the name `nfsroot-{machine}` where `{machine}` is the name of the platform that you are using. It also invokes the `exportfs` tool to publish the exported directory over the NFS server with the right permissions.

For example, if you are using the `AS5712` platform, a directory called `nfsroot-as5712` is created after you enter the `make deploy_nfsroot` command for deploying the NFS directory.

### Deploying your NFS directory
To deploy your NFS directory:
```bash
$ make deploy_nfsroot
```

Important notes:
* If you have previously deployed the NFS root directory, it will give an error message warning that the previous directory will be wipe-out. You will be able to press `CTRL+C` at that point or proceed. Any change that you did to that directory is lost.
* If you build the image again, the changes are not automatically deployed to the NFS root, you need to run the deploy target again after changing the image (see next section about how to use it with the `devenv` environment).

### Working with devenv on NFS
In the developer environment (`devenv`), there is a make target for every package named `<package>-nfs-deploy` that will deploy your modified code to the NFS root directory in a similar way that the `deploy-target` works.

To avoid always being prompted for the local user password, install your ssh-key in your own authorized keys:
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

### Booting your hardware with NFS
In platforms that use grub as boot loader, select the menu entry labeled `Switch Development -- NFS root` to boot your hardware in NFS root mode.

As explained previously, the bootloader that was flashed with the last installation set ups this entry to point to the IP address of the developer's machine and the path to the development directory. You can manually change these values by using the editor for the entry on the grub menu if required.
