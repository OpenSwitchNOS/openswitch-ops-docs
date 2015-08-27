# Using Vagrant box for OpenSwitch development
OpenSwitch provides a Vagrant file that allows you to provision a Vagrant box to use for OpenSwitch development:

Features:
* Runs on VirtualBox only.
* Tested in Windows, Linux and Mac OS X hosts.
* Enables by default shared home directory with the host.
* Sets up the proxies properly from the environment.
* Install the development packages.
* Creates an username equal to the used on the host.

## Contents
- [Common Pre-Setup](#Common-Pre-Setup)
- [Vagrant Setup](#Vagrant-Setup)
  - [Linux and Mac OS X](#Linux-and-Mac-OS-X)
  - [Windows](#Windows)
- [Common Post-Setup](#Common-Post-Setup)

## Common Pre-Setup
Download and install the following:
* Install [Vagrant](http://www.vagrantup.com/downloads.html)
* Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Vagrant Setup
Perform the steps for your operating system only and then proceed to the [Common Post-Setup](#common-post-setup) section.

### Linux and Mac OS X

1. Setup your proxy settings if you are behind a corporate network. These values will be setup inside the VM as well, so be sure to export them before creating the box with `vagrant up`.
```
$ export http_proxy=http://<proxy-url>:<proxy-port>
$ export port https_proxy=http://<proxy-url>:<proxy-port>
```

2. Download the Vagrant file
```
 $ curl -K https://git.openswitch.net/cgit/openswitch/ops-build/plain/tools/Vagrantfile >Vagrantfile
```

3. Provision the machine (will ask for the administrator password to setup the NFS share):
```
vagrant up
```

At this point you will be logged in the developer machine. Your home directory will be shared with the host machine, and the right file permissions will be used on both sides.

## Windows

1. Install [git](#https://git-scm.com/downloads) -it provides the ssh client required by Vagrant.

2. Download the [Vagrantfile](https://git.openswitch.net/cgit/openswitch/ops-build/plain/tools/Vagrantfile) and save the contents as a plain text with the name `Vagrantfile` in a suitable folder.

3. Open a `Windows Command Prompt` in `Administrator mode`
 * Press the Windows key `⊞ Win`
 * Type `cmd`
 * Press `Ctrl + ⇧ Shift + ↵ Enter` (this would open a new Command Prompt with a title that starts with `Administrator:`)

4. Setup your proxy settings if you are behind a corporate network:
```
set http_proxy=http://<proxy-url>:<proxy-port>
set https_proxy=http://<proxy-url>:<proxy-port>
```

5. Add ```git``` to your your `PATH`:
```
set PATH=%PATH%;C:\Program Files (x86)\Git\bin\
```

6. Change directory to the location where you saved the `Vagrantfile`:
```
cd c:\path\to\Vagrantfile
```

7. Provision the machine with the command
```
vagrant up
```
 * **NOTE:** If you get an error about the machine being in `powered-off` state, you may have to enable virtualization support in your BIOS. You can verify this in the VirtualBox Manager if you see an error related to `VT-x` being disabled. See [this link](http://superuser.com/questions/22915/how-do-i-enable-vt-x) for more information.
 * **NOTE:** Updating the software requires that the VM be restarted. If this is the case, the provisioning script will leave the VM powered off, and the last part of the output will prompt with a message to run `vagrant reload`. If you see this, run the command ```vagrant reload
```.

8. Finally, connect to the machine with the command
```
vagrant ssh
```

## Common Post-Setup
* (**Optional**) Harden your box: The default VM setup by this Vagrant configuration uses a private network (which means your machine is not accessible by the other computers on your network.) This can be changed by enabling a public network on the `Vagrantfile`, but before doing so, you need to harden your box by changing the default insecure SSH keys used by Vagrant and reconfiguring the `Vagrantfile`. If unsure on how to proceed, do not open a public network.
