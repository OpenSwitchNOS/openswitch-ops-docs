# Quick Start Guide

The following guide uses [VirtualBox](https://www.virtualbox.org/), [Vagrant](https://www.vagrantup.com/) and [Docker](https://www.docker.com) to provide an easy to setup development environment for the OpenSwitch project. The Vagrant image includes an Ubuntu VM with Docker already installed. Docker is used to run the OpenSwitch project image on a Linux container.

## What you get

* A VM running Ubuntu with the OpenSwitch development environment in `~/ops-build/`.
* An OpenSwitch x86_64 image built and ready to be used.
* An instance of OpenSwitch (the x86_64 image) running inside a Docker container.

**Note:** The username/password for the VM is the default vagrant/vagrant.

## Installation Steps
1. Download and install [VirtualBox](https://www.virtualbox.org/).
2. Download and install [Vagrant](https://www.vagrantup.com/).
3. Install the required Vagrant plugins. Use the following command:
``` bash
$ vagrant plugin install vagrant-reload
```
4. If you are behind a proxy, install the `vagrant-proxyconf` plugin:
```bash
$ vagrant plugin install vagrant-proxyconf
```

## Configuration  and installation steps
After following these steps, the system will be configured to work with OpenSwitch.
1. Download and unzip the [Vagrant files](https://github.com/shadansari/openswitch-vagrant/archive/master.zip) into a workspace directory.

2. If you are behind a proxy set the proxy `host:port` info in the `host/Vagrantfile`. The `host/Vagrantfile` is located in the workspace directory where you unzipped the Vagrant files.
```bash
config.proxy.http = "http://proxy.example.com:8080"
config.proxy.https = "https://proxy.example.com:8080"
```
3. Go to your workspace directory and run `vagrant up`.
```bash
$ cd openswitch-vagrant-master
$ vagrant up
```
**Note:** The first `vagrant up` can take a while since the entire OpenSwitch repository is fetched and built.



## Login to the OpenSwitch instance
After following the steps above, an OpenSwitch image has been built and Docker has been configured to run that image of OpenSwitch. Follow the steps below to connect to OpenSwitch. The steps below are to be performed in the newly created Ubuntu VM.

1. Run a shell on the Docker instance with `docker exec -ti ops bash`
2. Then run `vtysh` to start the OpenSwitch  CLI.

Example:

```bash
vagrant@ops-host:~/ops-build$ docker exec -ti ops bash
bash-4.3# vtysh
2015-09-01T23:31:47Z|00001|reconnect|INFO|unix:/var/run/openvswitch/db.sock: connecting...
2015-09-01T23:31:47Z|00002|reconnect|INFO|unix:/var/run/openvswitch/db.sock: connected
```

## Build System and Development Environment
After following these instructions, the newly created Ubuntu VM has been fully installed with the OpenSwith Build System and Development Environment. The OpenSwitch Build System assists in the development process by creating and maintaining an area containing a subset of the project's source files. It is a Git repository cloned to `~/ops-build` in your newly created Ubuntu VM. For details on how to manage it, see the [Development Environment](./dev-env.html) guide.



## More information
For more information on developing for OpenSwitch, see the following guides:
* [How to Contribute Code](./contrib-code.html) to learn how to submit your code UpStream.
* [Development Environment](./dev-env.html) to learn how to work with the OpenSwitch Development Environment.

## Notes
1. Has been tested on Windows 7 with Vagrant 1.7.4 and VirtualBox 5.0.
2. Latest versions of Vagrant and VirtualBox is recommended as Docker support is not present in older versions of Vagrant.