# The OpenSwitch Development Environment

In addition to building images, the OpenSwitch build system assists in the development process by creating and maintaining an area containing a subset of the project's source files.  Once an OpenSwitch repository has been cloned and configured, working in the development environment may begin, i.e. no need to build the image.

The OpenSwitch development environment is built upon the `devtool` recently written by Paul Eggleton.

**All of the packages in OpenSwitch are not yet supported in the development environment.**

- [The OpenSwitch Development Environment](#the-openswitch-development-environment)
	- [Managing the development environment](#managing-the-development-environment)
	- [Prerequisites for using Development Environment](#prerequisites-for-using-development-environment)
	- [devenv_init](#devenvinit)
	- [devenv_list_all](#devenvlistall)
	- [devenv_status](#devenvstatus)
	- [devenv_add](#devenvadd)
	- [devenv_rm](#devenvrm)
	- [devenv_update_recipe](#devenvupdaterecipe)
	- [devenv_clean](#devenvclean)
	- [devenv_cscope](#devenvcscope)
	- [devenv_import](#devenvimport)
	- [devenv_patch_recipe](#devenvpatchrecipe)
	- [devenv_refresh](#devenvrefresh)
	- [git_pull](#gitpull)
- [Working with the Modified Packages](#working-with-the-modified-packages)
	- [Building and Cleaning](#building-and-cleaning)
	- [Building Documentation](#building-documentation)
	- [Deploying and Undeploying](#deploying-and-undeploying)
	- [Troubleshooting](#troubleshooting)
	- [Understanding the Building System Infrastructure](#understanding-the-building-system-infrastructure)
- [Working with branches](#working-with-branches)
- [How to add new code to OpenSwitch](#how-to-add-new-code-to-openswitch)
	- [Adding a New Repository](#adding-a-new-repository)
	- [C/C++ code with CMake](#cc-code-with-cmake)
		- [Writing a new daemon with CMake](#writing-a-new-daemon-with-cmake)
	- [Adding a recipe for your daemon](#adding-a-recipe-for-your-daemon)
- [How to setup an NFS root environment for development](#how-to-setup-an-nfs-root-environment-for-development)
	- [Introduction](#introduction)
	- [Requirements](#requirements)
	- [Using NFS root](#using-nfs-root)
		- [Configure your NFS server IP address in the image](#configure-your-nfs-server-ip-address-in-the-image)
		- [Deploy your NFS directory](#deploy-your-nfs-directory)
		- [Working with devenv on NFS](#working-with-devenv-on-nfs)
		- [Boot your hardware with NFS](#boot-your-hardware-with-nfs)

### Managing the development environment

The area for developing software will be found under the `src/` directory at the top level of the workspace.  The subdirectories will contain the source for the packages being worked with.  All targets for the development environment management begin with `devenv_` and can be shown with tab-completion:
```bash
$ make devenv_<TAB><TAB>
devenv_add            devenv_clean          devenv_import         devenv_init
devenv_patch_recipe   devenv_rm             devenv_status         devenv_update_recipe
```

#### Prerequisites for using Development Environment
* Follow the [Getting Started](./getting-started.md) guide to configure your system.
* Review the [How to contribute to the OpenSwitch Project Code](./contrib-code.md) guide.
* Follow the [OpenSwitch Coding Style](./contrib-code.md#openswitch-coding-style) for new code, or follow the existing style in non-OpenSwitch modules.

#### devenv_init
Initiates the development environment.
```bash
$ make devenv_init
```
If git-review has been installed, `make`-ing the `devenv_init` target will configure the workspace for possible future change submissions.

#### devenv_list_all
This command is in charge of show the user the list of projects to use with the platform that has been configured.
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

#### devenv_status
The `devenv_status` target may be used to show the status of the development environment.
```bash
$ make devenv_status
ops-vland: <path>/src/ops-vland
```

#### devenv_add
Packages to be developed may be added to the collection, one or more at a time, as shown in the following example.  As the packages are added, each one will be fetched into the development `src/` directory in separate sub-directories. Each package is also unpacked and patched (as required).
```bash
$ make devenv_init
...
$ make devenv_add ops-vland ops-pmd
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
ops-vland: <path>/ops-build/src/ops-vland
```

#### devenv_rm
Complementary to the `devenv_add` target, the `devenv_rm` target can be used to remove packages from the collection.  Continuing from the previous example:
```bash
$ make devenv_rm ops-vland
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
```
**Note**: Unsaved changes to the source in the removed package will be irretrievably lost.

#### devenv_update_recipe
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
The complementary target to `devenv_init`, this target will remove any/all package source as well as the `src/` directory.
**Note**: Unsaved changes to the source in any/all packages will be irretrievably lost.

#### devenv_cscope
A tool for browsing in development enviroment, it has the same features as `cscope` for Linux, and it is necessary to install the `cscope` tool for Linux before using `devenv_cscope`.
```bash
$  make devenv_cscope
```

#### devenv_import
This command will help the developer to import code, you have to specify the name of the package that you want to import
```bash
$  make devenv_import <package_name>
```

#### devenv_patch_recipe
If the user is working with a recipe and this recipe is not from OpenSwitch; after the user made the commit it will run this command in order to create the necessary patch for the OpenSwitch environment.
```bash
$  make devenv_patch_recipe
```

#### devenv_refresh
This command performs a `git pull --rebase` in all your devenv repos. If you have local changes not staged the git pull will fail, and you will get a warning in red to let you know that particular repo wasn’t updated.
```bash
$  make devenv_refresh
```

#### git_pull

This command performs a `git pull --rebase` in the base Git repo and the overlay repos, and if you have a devenv, also on all the repos there (like the previous command).
```bash
$  make git_pull
```

### Working with the Modified Packages
All packages in the workspace will have targets to build, clean, deploy or undeploy, which are formed by prefixing these terms with the package name (can be seen with tab completion). Example:
```bash
$ make devenv_init
...
$ make devenv_add ops-vland
...
$ make ops-vland-<TAB><TAB>
ops-vland-build     ops-vland-clean     ops-vland-deploy    ops-vland-undeploy
```

#### Building and Cleaning
After (or even before) modifying the source for a package, the package can be built by using the target with the `-build` suffix. **Note:** The `-clean` suffixed target for the package is intended to return the package to a clean state, but is not functional at this time.

**Note:** The build system will ensure that any prerequisites for this target are updated before the package build takes place.

#### Building Documentation
The OpenSwitch project uses the Markdown markup language to generate its documentation. To build the OpenSwitch documentation you will need to install `markdown`.

After `markdown` is installed, the documentation can be built by `make`-ing `dist-docs` as seen below.
```bash
make devenv_init
make devenv_add ops-vland
cd src/ops-vland/build
make dist-docs
```

#### Deploying and Undeploying
By specifying a running target, accessible via SSH, the results of the built package can be installed using the `-deploy` target. The example below will use SSH as user root to install the results of build vland on the device at address 10.0.2.5:


```bash
$ make vland-deploy TARGET="root@10.0.2.5"
```


To display the deployment progress add the `-s` option to the TARGET, e.g. `TARGET="-s root@10.0.2.5"`.
To do a dry-run of the deployment without actually deploying the content use `-n`, e.g. `TARGET="-n root@10.0.2.5"`.

**Note:** Deployment of files will fail if they are "busy" on the target.

Undeploying a package will remove the content from the target device.  It does not replace or restore the original content.  The syntax is similar to that used for deploying.

After you're satisfied with your modifications, see the [Contribute Code](./contribute-code.html) guide.

#### Troubleshooting
While building the project you may encounter disk space error messages, such as the  following:
```bash
ERROR: No space left on device or exceeds fs.inotify.max_user_watches?
ERROR: To check max_user_watches: sysctl -n fs.inotify.max_user_watches.
ERROR: To modify max_user_watches: sysctl -n -w fs.inotify.max_user_watches=<value>.
```
The problem could be caused by low disk space available in your system. Make sure that you have enough free disk space(e.g `df -h`):
```bash
$ df -h
Filesystem                                Size  Used Avail Use% Mounted on
/dev/mapper/lvm_vg-lvm_root_lv  457G  146G  288G  34% /
none                                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                                      7.8G  4.0K  7.8G   1% /dev
...omitted output
```

If enough disk space is available, modify the file `/etc/sysctl.d/30-tracker.conf`. The recommendation is to multiple current value of `fs.inotify.max_user_watches` by 2.


#### Understanding the Building System Infrastructure

The building system is based in the [Yocto Project](https://www.yoctoproject.org/about). We will not cover how Yocto works but will include some important aspects that are related with OpenSwitch.

After you download or clone OpenSwitch in your machine, you can see the followings folders and files:

```bash
$ ls
build/  COPYING  images/  Makefile  README.md  src/  tools/  yocto/
```
**build/**: This directory contains user configuration files and the output generated by Open Embedded; it is standard configuration.

**COPYING**: Licensing information. Check more details at the [Apache License](http://www.apache.org/licenses/LICENSE-2)

**images/**: Symbolic links to all the images that have been built. The most important file is `openswitch-disk-image-<platform>.tar.gz`

**Makefile**: The OpenSwitch Makefile.

**tools/**: This directory contains tools that are primarily used by the build system. In this folder we can find the `Rules.make` file, it is the core of our `Makefile`.

**yocto/**: The core of the build system. Inside this directory :

```bash
$ ls
openswitch/  poky/
```

**poky/**: This folder is the core of the Yocto project. Read more about it [YoctoProject Documentation](http://www.yoctoproject.org/docs/1.6/dev-manual/dev-manual.html)

**openswitch/**: This folder is our core and you can find the following information there:

```bash
$ ls
Rules.make  meta-distro-openswitch/  meta-platform-openswitch-as5712/
meta-foss-openswitch/    meta-platform-openswitch-genericx86-64/
```

**meta-foss-openswitch/**: This folder contains all the recipes and patch for FOSS (**Free and Open Source Software**)

**meta-distro-openswitch/**: This folder contains all the recipes and patch related to the distribution. It means that you can find all the recipes related with the core and the kernel of OpenSwitch

**meta-platform-openhalon-XXX/**: This folder contains all the recipes and patch related to specific platform, for example: `meta-platform-openswitch-genericx86-64` contains all the things that Yocto needs in oder to build a new image for x86 platform.

Inside of every folder we have some structure in order to keep everything in order, for example inside  `meta-platform-openswitch-genericx86-64` we have folder for core `recipes`,`kernel recipes`  and some others. It means if you have to change something in the kernel and the change is just for x86 you have to go inside `yocto/openswitch/meta-platform-openswitch-genericx86-64/recipes-kernel` and add your recipe or the `bbapend` file.

### Working with branches
If you need to work with development branches there are a couple of changes that will be required in the workflow. Usually only owners of the respective git repo (project owners) or the members of certain privileged user groups can create new feature branches, and these branches are restricted to have the prefix `feature/` on it.

For example assume that you are starting to work on the branch `foo` on the project `sysd`. If you are the developer creating the branch you will need to use the following commands:

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

If you are a developer working on a feature branch (foo) that already exists, your workflow will look like:

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
git checkout [target]
git pull # update the local repo to find any new remote branches
git merge --no-ff [source] # merge the source branch changes to the target
git commit -s --amend # this will modify the commit message to include a ChangeId. Without the ChangeID, commits will not go through
git review [target] # send the review for target branch. The review for merge will not have any code as this is only a merge
```

## How to add new code to OpenSwitch
OpenSwitch preferred build system for C/C++ applications is `CMake`. But as the code start using other languages, guidelines will be provide here for them

### Adding a New Repository
The following are the steps to follow in order to add a new repo to the OpenSwitch project, referred as `<repo-name>`.

1) Git clone project:
```bash
git clone https://review.openswitch.net/infra/project-config
```

2) Create a new file `./gerrit/acls/openswitch/<repo-name>.config` with the the following contents. **Note:** The code review and abandon group name should be `<repo-name>-maintainers` (e.g if the repo name is `ops-vland`, then the group name would be `ops-vland-maintainers`).
```
[access "refs/heads/*"]
abandon = group Change Owner
abandon = group <repo-name>-maintainers
label-Code-Review = -2..+2 group <repo-name>-maintainers
label-Workflow = -1..+1 group Change Owner
```

3) The `<repo-name>-maintainers` group for your repo will get created by the `project-config-maintainers` when they approve the code review.

4) Modify `gerrit/projects.yaml` to add the repository.
```
- project: openswitch/<repo-name>
  description: <Repo Description>
```

5) Git commit the changes. In the commit message please specify the `GitHub` full name of two users that you wish to add as maintainers of this repo.
```bash
git commit --signoff
```

6) Git review the changes
```bash
git review
 ```

### C/C++ code with CMake
#### Writing a new daemon with CMake
The following is an example code
```
# Copyright (C) 2015 Hewlett Packard Enterprise Development LP
# All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may
# not use this file except in compliance with the License. You may obtain
# a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations
# under the License.

cmake_minimum_required (VERSION 2.8.1)

PROJECT(mydaemon)

# Find dependencies using pkg-config
include(FindPkgConfig)
pkg_check_modules(HALONUTILS halonutils)
pkg_check_modules(SYSTEMD systemd)
pkg_check_modules(OVSCOMMON libovscommon)
pkg_check_modules(OVSDB libovsdb)

######## Build and include settings ########
include_directories(
	${PROJECT_BINARY_DIR} ${PROJECT_SOURCE_DIR}
        ${OVSCOMMON_INCLUDE_DIRS}
)

file(GLOB SOURCES
	"src/*.c"
)

add_executable(
	mydaemon
	${SOURCES}
)

target_link_libraries (mydaemon ${HALONUTILS_LIBRARIES}  ${OVSCOMMON_LIBRARIES}
		        ${OVSDB_LIBRARIES}  ${SYSTEMD_LIBRARIES} -lpthread -lrt)

# Install the daemon on bin directory
INSTALL(TARGETS mydaemon
	RUNTIME DESTINATION bin
)
```

####  Adding a recipe for your daemon
This is an example of a recipe

```
SUMMARY = "Halon My Daemon"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

DEPENDS = "halonutils halon-ovsdb"

SRC_URI = "git://openswitch.net/openhalon/mydaemon;protocol=http"

SRCREV="${AUTOREV}"

S = "${WORKDIR}/git"

inherit cmake
```

The recipes have some important parts:

* **SUMMARY** = It is just a summary of your recipe.
* **LICENSE** = It is the license for your recipe.
* **LIC_FILES_CHKSUM** = It is the path of the license file with the md5.
* **DEPENDS** = In this label we have to include all the dependency that our recipe have in order to compile in this case is depending on `halonutils` and `halon--ovsdb`
* **SRC_URI** = This is the list of source files, it can be local or remote, Yocto supports several
 protocols. You can find the complete list in the [Yocto Ref Manual](http://www.yoctoproject.org/docs/current/ref-manual/ref-manual.html)
* **S** = This is the location in the build directory where the recipe source code resides. It is the work directory.
* **inherit** = You can use this variable to inherit the functionality of a class, in our case we need `cmake` in order to build this recipe.

## How to setup an NFS root environment for development
The OpenSwitch build environment supports working with an NFS Root Setup that speeds up development workflows. This section provides the instructions to set it up.

### Introduction
NFS root environment is a setup where your target hardware boots his root file system from a directory on the developer's machine shared over NFS (Network File System). This setup is very convenient since it requires almost no effort to update the code/configuration on the target when developers are making changes.

### Requirements
* A Linux host machine that has an NFS server installed.

### Using NFS root
The OpenSwitch build system includes logic that simplifies working with NFS root configuration. In order to use it you will require to perform some steps detailed below.

#### Configure your NFS server IP address in the image
By default the build system will create a debug entry on the boot menus of the switch to start with an NFS root, you need to configure the IP address and path to your NFS root for the menu. To do this, use the  file `build/nfs.conf` on the build directory.

For example to enable automatic detection of the IP address from the main subnet used to reach the Internet on your host machine and the default file system deploy location use:
```bash
echo 'NFS_SERVER_IP ??= "${@get_default_ip(d)}"' > build/nfs.conf
echo 'NFS_SERVER_PATH ??= "${BUILD_ROOT}/nfsroot-${MACHINE}"' >> build/nfs.conf
```
You can also specify the IP address directly.

**NOTE:** After changing this file you will need to rebuild your image and re-flash it on the target hardware. As alternative you can manually modify the address on the bootloader before booting.

#### Deploy your NFS directory
The build system includes a make target that will deploy the root file system into a directory with the name `nfsroot-{machine}` where `{machine}` is the name of the platform that you are using. It will also invoke the `exportfs` tool to publish the exported directory over the NFS server with the right permissions.

For example, if you are using the `AS5712`, you will get a directory called `nfsroot-as5712` with the following command:
```bash
$ make deploy_nfsroot
```

Important notes:
* If you have previously deployed the NFS root directory, it will give an error message warning that the previous directory will be wipe-out. You will be able to`CTRL+C` a that point or proceed. Any change that you did to that directory will be lost.
* If you build the image again, the changes are not automatically deployed to the NFS root, you need to run the deploy target again after changing the image (see next section about how to use it with the `devenv` environment).

#### Working with devenv on NFS
If you are using the developer environment (`devenv`), you will find a make target for every package named `<package>-nfs-deploy` that will deploy your modified code to the NFS root directory in a similar way that the `deploy-target` works.

This may keep asking for the local user password, to avoid this, you can install your ssh-key in your own authorized keys:
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

#### Boot your hardware with NFS
In platforms that use grub as boot loader you will find a menu entry labeled `Switch Development -- NFS root`. Select that entry to boot your hardware in NFS root mode.

As explained above, the bootloader that was flashed with the last installation will setup this entry to point to the IP address of the developer's machine and the path to the development directory. You can manually change these values using the editor for the entry on the grub menu if required.
