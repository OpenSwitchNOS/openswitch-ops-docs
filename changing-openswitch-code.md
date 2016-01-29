# Changing OpenSwitch Code

##Contents

- [Overview](#overview)
- [Prerequisites for using the development environment](#prerequisites-for-using-the-development-environment)
- [Managing the development environment](#managing-the-development-environment)
	- [Workflows](#workflows)
		- [Working with one repository](#working-with-one-repository)
		- [Component Testing](#component-testing)
		- [Reconfigure to a different platform](#reconfigure-to-a-different-platform)
	- [Development commands](#development-commands)
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
	- [Prerequisites](#prerequisites)
	- [Using NFS root](#using-nfs-root)
		- [Configuring the NFS server IP address in the image](#configuring-the-nfs-server-ip-address-in-the-image)
		- [Directory structure of the deployed NFS directory](#directory-structure-of-the-deployed-nfs-directory)
		- [Deploying your NFS directory](#deploying-your-nfs-directory)
		- [Working with devenv on NFS](#working-with-devenv-on-nfs)
		- [Booting the hardware with NFS](#booting-the-hardware-with-nfs)


##Overview
In addition to building images, the OpenSwitch build system assists in the development process by creating and maintaining an area that contains a subset of the project's source files.  Once an OpenSwitch repository has been cloned and configured, working in the development environment may begin; there is no need to build the image.

The OpenSwitch development environment is built upon `devtool`, which was recently added to Yocto.

**Not all of the Yocto packages in OpenSwitch are supported in the development environment.**


## Prerequisites for using the development environment
1. Follow the [Quick Start](quick-start) guide to configure your system.
2. Review the [How to contribute to the OpenSwitch Project Code](contribute-code) guide.
3. Follow the [OpenSwitch Coding Style](contribute-code#openswitch-coding-style) for new code, or follow the existing style in non-OpenSwitch modules.

## Managing the development environment

All targets for the development environment management begin with `devenv_`, and can be shown with tab-completion:
```
$ make devenv_<TAB><TAB>
devenv_add            devenv_clean          devenv_import         devenv_init
devenv_patch_recipe   devenv_rm             devenv_status         devenv_update_recipe
```

Detailed information about each available target is provided below.

The area for developing software is under the `src/` directory at the top level of the workspace. Use `make devenv_add <package>` to fetch packages and corresponding source code to `src/`. The subdirectories contain the source for the packages.

### Workflows
There are basic scenarios in the development environment where the `devenv_` commands are used. This section provides workflow examples for those commands.

#### Working with one repository

1. Clone OpenSwitch repo, configure the platform, and compile the code.
```
git clone https://git.openswitch.net/openswitch/ops-build
cd ops-build
make configure genericx86-64
make
```
1. Setup the sandbox before adding other repositories to the environment.
```
make devenv_init
```
1. Add the needed repository, for example:
```
make devenv_add ops-vland
```
The added repository can be found in `src/`.
1. Make changes to the repository.
1. Build the component code with the following command:
```
make ops-vland-build
```
1. To clean a repository from `src/`, if needed:
```
make ops-vland-clean
```
**Note**: Unsaved changes to the source in any/all packages are lost.
1. To deploy the newly built image to a docker container switch:
```
make ops-vland-deploy root@<IP of docker container>
```
**Note**: Remember to run `systemctl-daemon reload ` on the docker container to pick up these changes.
If the build succeeds, doing a `make` builds the new image with the changes.
1. To remove a repository from `src/`, if needed:
```
make devenv_rm ops-vland
```

#### Component Testing
Component tests are run using `pytest`.
* Export the docker image:
```
make export_docker_image
```
* Set up the sandbox for running tests:
```
make devenv_ct_init
```
* Run all tests inside the repositories in the `src/` directory:
```
make devenv_ct_test
```
**Note**: This runs all scripts that have the format: `test_xxx.py`.

* To run an individual test case, specify it on the command line:
```
make devenv_ct_test src/ops/tests/bgp/test_bgp_ft_basic_bgp_route_advertise.py
```

#### Reconfigure to a different platform
1. Configure the new platform.
```
make switch-platform genericx86-64
```
1. Build the new platform.
```
make
```

### Development commands
This section provides a complete list of all the development commands that can be used when contributing code to the OpenSwitch project.

#### devenv_init
Initiates the development environment.
```
$ make devenv_init
```
If git-review has been installed, making the `devenv_init` target configures the workspace for possible future change submissions.

#### devenv_list_all
This command shows the list of available packages for use with a configured platform.
```
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
The `devenv_status` target shows the status of the development environment.
```
$ make devenv_status
ops-vland: <path>/src/ops-vland
```

#### devenv_add
Packages to be developed can be added to the collection, one or more at a time, by using this command, as shown in the following example. As the packages are added, each one is fetched into the development `src/` directory in separate sub-directories. Each package is also unpacked and patched as required.
```
$ make devenv_init
...
$ make devenv_add ops-vland ops-pmd
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
ops-vland: <path>/ops-build/src/ops-vland
```

#### devenv_rm
Complementary to `devenv_add`, the `devenv_rm` target is used to remove packages from the collection, as shown in the following example:
```
$ make devenv_rm ops-vland
...
$ make devenv_status
ops-pmd: <path>/ops-build/src/ops-pmd
```
**Note**: Unsaved changes to the source in the removed package are lost.

#### devenv_update_recipe
The `devenv_update_recipe` command is used to update build recipes after changes have been committed to a package in the development environment.  The workflow is as follows:
```
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
The complementary target to `devenv_init` is `devenv_clean`. `devenv_clean` removes all package sources, in addition to the removal of the `src/` directory.
**Note**: Unsaved changes to the source in any/all packages are lost.

#### devenv_cscope
`devenv_cscope` is a tool for browsing in a development enviroment. `devenv_cscope` has the same features as `cscope` for Linux. It is necessary to install the `cscope` tool for Linux before using `devenv_cscope`.
```
$  make devenv_cscope
```

#### devenv_import
`devenv_import` imports code. Specify the name of the package to be imported.
```
$  make devenv_import <package_name>
```

#### devenv_patch_recipe
If a committed recipe is not from OpenSwitch, run the `devenv_patch_recipe` command after the commit to create the necessary patch for the OpenSwitch environment.
```
$  make devenv_patch_recipe
```

#### devenv_refresh
This command performs a `git pull --rebase` in all of the devenv repositories. If there are local changes not staged, the git pull fails, and a warning in red shows that the particular repository was not updated.
```
$  make devenv_refresh
```

#### git_pull

This command performs a `git pull --rebase` in the base Git repository and the overlay repositories. This command also works with the `devenv` environment and all of its repositories, similar to `devenv_refresh`.
```
$  make git_pull
```

## Build system infrastructure

The build system is based in the [Yocto Project](https://www.yoctoproject.org/about). This document does not cover how Yocto works, but does include some important aspects related to OpenSwitch.

The following directories and files are displayed after cloning OpenSwitch on the machine:

```
$ ls
build/  COPYING  images/  Makefile  README.md  src/  tools/  yocto/
```
- **build/**: This directory contains user configuration files and the output generated by Open Embedded; it is standard configuration.

- **COPYING**: Licensing information. For more details, see the [Apache License](http://www.apache.org/licenses/LICENSE-2.0).

- **images/**: Symbolic links to the images that have been built. The most important file is `openswitch-disk-image-<platform>.tar.gz`

- **Makefile**: The OpenSwitch Makefile.

- **tools/**: This directory contains tools primarily used by the build system. This directory includes the `Rules.make` file, which is the core of `Makefile`.

- **yocto/**: The core of the build system. Inside this directory:

```
$ ls
openswitch/  poky/
```

- **poky/**: This directory is the core of the Yocto project. For more information, see [YoctoProject Documentation](http://www.yoctoproject.org/docs/1.6/dev-manual/dev-manual.html).

- **openswitch/**: This directory is the core OpenSwitch directory. Its contents are shown in the following example:

```
$ ls
Rules.make  meta-distro-openswitch/  meta-platform-openswitch-as5712/
meta-foss-openswitch/    meta-platform-openswitch-genericx86-64/
```

- **meta-foss-openswitch/**: This directory contains all of the recipes and the patch for FOSS (**Free and Open Source Software**).

- **meta-distro-openswitch/**: This directory contains all of the recipes and patches related to the distribution, including all recipes related to the core and kernel of OpenSwitch.

- **meta-platform-openhalon-XXX/**: This directory contains all of the recipes and the patch related to a specific platform. For example, `meta-platform-openswitch-genericx86-64` contains all of the code Yocto requires to build a new image for the x86 platform.

Every directory contains a structure to keep its contents in order. For example, the  `meta-platform-openswitch-genericx86-64` directory contains subdirectories for core `recipes` and `kernel recipes`, in addition to other subdirectories. For example, if a change is needed in the kernel for  just x86, open `yocto/openswitch/meta-platform-openswitch-genericx86-64/recipes-kernel` and add the recipe or the `bbapend` file.

## Working with modified packages
All packages in the workspace have targets to `build`, `clean`, `deploy` or `undeploy`, which are formed by prefixing the package name to the operation, as shown in the following example:
```
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
By specifying a running target, accessible via SSH, the results of the built package can be installed using the `-deploy` target. The following example uses SSH as the user root to install the results of build vland on a device at address 10.0.2.5:

```
$ make vland-deploy TARGET="root@10.0.2.5"
```

To display the deployment progress, add the `-s` option to `TARGET`, for example, `TARGET="-s root@10.0.2.5"`.
To do a dry-run of the deployment without actually deploying the content, use `-n`, for example, `TARGET="-n root@10.0.2.5"`.

**Note:** Deployment of files fail if they are "busy" on the target.

### Undeploying a package
Undeploying a package removes the content from the target device.  It does not replace or restore the original content.  The syntax is similar to that used for deploying.

### Building the documentation
The OpenSwitch project uses the Markdown markup language to generate its documentation. To build the OpenSwitch documentation, install `markdown`.

After `markdown` is installed, the documentation can be built by makeing `dist-docs` as shown in the following example:
```
make devenv_init
make devenv_add ops-vland
cd src/ops-vland/build
make dist-docs
```

### Troubleshooting
While building the project, disk space error messages might appear, such as the following:
```
ERROR: No space left on device or exceeds fs.inotify.max_user_watches?
ERROR: To check max_user_watches: sysctl -n fs.inotify.max_user_watches.
ERROR: To modify max_user_watches: sysctl -n -w fs.inotify.max_user_watches=<value>.
```
The problem could be caused by not enough disk space available on the system. Ensure there is enough free disk space by using the `df -h` command, as shown in the following example:
```
$ df -h
Filesystem                                Size  Used Avail Use% Mounted on
/dev/mapper/lvm_vg-lvm_root_lv  457G  146G  288G  34% /
none                                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                                      7.8G  4.0K  7.8G   1% /dev
...omitted output
```

If enough disk space is available, modify the file `/etc/sysctl.d/30-tracker.conf`. The recommendation is to multiply the value of `fs.inotify.max_user_watches` by two.


## Working with branches
All gerrit registered users have permission to create new feature branches, and these branches are restricted to have the prefix `feature/`.

For example, to create a branch `foo` on the project `sysd`, use the following commands:

```
make devenv_add sysd
cd src/sysd
git review -s # Setup the Gerrit remote
git checkout -b feature/foo # The -b flag creates the new branch
git push gerrit feature/foo # Publishes the new branch
# Do changes needed
git commit -s # This step is needed for branch creation even if you do not have any code changes
git review feature/foo # Send the review for this branch
```

If working on a feature branch that already exists, for example `foo`, the workflow would resemble the following example:

```
make devenv_add sysd
cd src/sysd
git pull --rebase # update the local repo to find any new remote branches
git checkout -b feature/foo --track origin/feature/foo
git branch -u origin/feature/foo
# do your changes and commit
git review feature/foo # send the review for this branch
```

To merge a branch (source) with another branch (target):

```
make devenv_add sysd
cd src/sysd
git checkout <target>
git pull # update the local repo to find any new remote branches
git merge --no-ff <source> # merge the source branch changes to the target
git commit -s --amend # this will modify the commit message to include a ChangeId. Without the ChangeID, commits will not go through
git review <target> # send the review for target branch. The review for merge will not have any code as this is only a merge
```

## Setting up an NFS root environment for development
The OpenSwitch build environment supports working with an NFS Root Setup, which speeds up development workflows. This section provides instructions to set it up.

### Introduction
NFS root environment is a setup where the target hardware boots its root file system from a directory on the developer's machine that is shared over the NFS (Network File System). This setup is convenient since it requires little effort to update the code/configuration on the target when developers make changes.

### Prerequisites
A Linux host machine that has an NFS server installed.

### Using NFS root
The OpenSwitch build system includes logic that simplifies working with an NFS root configuration.

#### Configuring the NFS server IP address in the image
By default, the build system creates a debug entry on the boot menus of the switch to start with an NFS root.
1. Configure the IP address and path to the NFS root for the menu by modifying the file `build/nfs.conf` on the build directory.

 For example, to enable automatic detection of the IP address from the main subnet used to reach the Internet on the host machine and the default file system deploy location, ensure the following is in the file:
```
echo 'NFS_SERVER_IP ??= "${@get_default_ip(d)}"' > build/nfs.conf
echo 'NFS_SERVER_PATH ??= "${BUILD_ROOT}/nfsroot-${MACHINE}"' >> build/nfs.conf
```
The IP address can also be directly specified.

2. Rebuild the image and re-flash it on the target hardware. Alternatively, the address can be manually modified on the bootloader before booting. <!--How would you do this? -->

#### Directory structure of the deployed NFS directory
The build system includes a make target that deploys the root file system into a directory with the name `nfsroot-{machine}`, where `{machine}` is the name of the platform that is being used. It also invokes the `exportfs` tool to publish the exported directory over the NFS server with the right permissions.

For example, if the `AS5712` platform is used, a directory called `nfsroot-as5712` is created after the `make deploy_nfsroot` command is entered for deploying the NFS directory.

#### Deploying your NFS directory
To deploy the NFS directory:
```
$ make deploy_nfsroot
```

Important notes:
* If the NFS root directory has been previously deployed, an error message warning that the previous directory will be wiped-out appears. Press `CTRL+C` at that point or proceed. Any changes to that directory are lost.
* If the image is built again, the changes are not automatically deployed to the NFS root. Run the deploy target again after changing the image (see the next section about how to use it with the `devenv` environment).

#### Working with devenv on NFS
In the developer environment (`devenv`), there is a make target for every package named `<package>-nfs-deploy` that deploys the modified code to the NFS root directory in a similar way to how `deploy-target` works.

To avoid always being prompted for the local user password, install an ssh-key in the authorized keys:
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

#### Booting the hardware with NFS
In platforms that use grub as the bootloader, select the menu entry labeled `Switch Development -- NFS root` to boot the hardware in NFS root mode.

As previously explained, the bootloader that was flashed with the last installation, sets up this entry to point to the IP address of the developer's machine and the path to the development directory. These values can be manually changed by using the editor for the entry on the grub menu, if required.

