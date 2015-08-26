# Using Vagrant box for OpenHalon development
OpenSwitch provides a Vagrant file that allows you to provision a Vagrant box to use for OpenSwitch development:

Features:
* Runs on VirtualBox only.
* Tested in Windows, Linux and Mac OS X hosts.
* Enables by default shared home directory with the host (virtualbox in Windows, NFS on Linux/OSX).
* Sets up the proxies properly from the environment.
* Install the development packages and Eclipse IDE with relevant plug-ins.
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

* Setup your proxy settings if you are behind a corporate network. These values will be setup inside the VM as well, so be sure to export them before creating the box with 'vagrant up'. The correct syntax of the http and https proxies should include the complete URL including the protocol and port, for example: `http://myproxy.domain.com:8080`
```
$ export http_proxy=http://<proxy.fqdn>:<port>
$ export port https_proxy=http://<proxy.fqdn>:<port>
```

* Download the Vagrant file
```
 $ curl -K https://git.openswitch.net/cgit/openswitch/ops-build/plain/tools/Vagrantfile >Vagrantfile
```

* Provision the machine (will ask for the administrator password to setup the NFS share):
```
$ vagrant up
```

At this point you will be inside the developer machine with the vagrant user. The folder `/home/<username>` will be NFS shared with the host machine, and the right file permissions will be used in both sides.

## Windows

* Install `Git`. It provides an `ssh.exe` required to simplify working with Vagrant.
* Download the [Vagrantfile](https://git.openswitch.net/cgit/openswitch/ops-build/plain/tools/Vagrantfile) and save the contents as a plain text with the name `Vagrantfile`
* Open a `Windows Command Prompt` in `Administrator mode`
 * Press the Windows key `⊞ Win`
 * Type `cmd`
 * Press `Ctrl + ⇧ Shift + ↵ Enter`
 * This would open a new Command Prompt with a title that starts with `Administrator:`
* Setup your proxy settings if you are behind a corporate network:
```
set http_proxy=http://<proxy.fqdn>:<port>
set https_proxy=http://<proxy.fqdn>:<port>
```
* Setup your `PATH` to include `ssh.exe` on it:
```
set PATH=%PATH%;C:\Program Files (x86)\Git\bin\
```
* Change directory to the location where you saved the `Vagrantfile`, example:
```
cd c:\Users\MyUser
```
* Provision the machine with the command below.
```bash
vagrant up
```
 * **NOTE:** If you get an error about the machine being in `powered-off` state, you may have to enable virtualization support in your BIOS. You can verify this in the VirtualBox Manager if you see an error related to `VT-x` being disabled. See [this link](http://superuser.com/questions/22915/how-do-i-enable-vt-x) for more information.
* The VM is now setup with the latest version of Linux. Updating the software sometimes requires that the VM be restarted to make use of it. If this is the case, the provisioning script will leave the VM powered off, and the last part of the output will prompt with a message to run` vagrant reload` to use the machine. If you see this, then run the command:
```bash
vagrant reload
```
 * If the prompt to reload isn't there, the machine should be left running and ready for use. If you don't notice and issue the reload anyway, no harm done.
* Finally, connect to the machine with the command:
```bash
vagrant ssh
```

## Common Post-Setup
Perform the following setups for any OS (Linux, OSX, Windows).

Login into the Vagrant system (with `vagrant ssh`):
* (**Optional**) Harden your box.
  * The default VM setup by this Vagrant configuration uses a private network (which means your machine is not accessible by the other computers on your network.) This can be changed by enabling a public network on the Vagrantfile, but before doing so, you need to harden your box by changing the default insecure SSH keys used by Vagrant and reconfiguring the Vagrantfile. If unsure on how to proceed, do not open a public network.
* Configure your Git name and email
  * Open Git-Bash in Windows, or any Command Line in Linux or OSX
  * Run the following commands  to set-up your name and email
```bash
$ git config --global user.name "Joe Doe"
$ git config --global user.email "your_email@example.com"
  ```
