# Setup a Linux machine for OpenSwitch Development
OpenSwitch requires a Linux based system in order to contribute to the source code an build OpenSwitch images. If a contributor wishes to contribute to the OpenSwitch documentation only, a Windows based O.S can be used. Refer to the [How to contribute to the OpenSwitch documentation in Windows](./windows-setup.html) guide for instructions.

To configure your Linux based system in order to contribute to the OpenSwitch project or to be able to build an OpenSwitch image, follow the instructions below.

## Contents

- [Define proxies](#Define-Proxies)
- [Install the required packages](#Install-the-required-packages)
  - [Ubuntu](#Ubuntu)
  - [Debian](#Debian)
  - [Fedora](#Fedora)
  - [CentOS](#CentOS)

## Define proxies
Define your proxies if in a network that uses a proxy to reach the Internet. Otherwise, skip to the next step. For example:
```bash
$ export http_proxy=http://<proxy-url>:<proxy-port>
$ export https_proxy=https://<proxy-url>:<proxy-port>
$ export ftp_proxy=ftp://<proxy-url>:<proxy-port>
```
* Consider adding the export commands to a location where they will be setup automatically, for example at `~/.bashrc`.

## Install the required packages
The packages required vary depending on the Linux distributions. Run the installation commands for your distribution.

### Ubuntu
```bash
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib  build-essential chrpath screen curl device-tree-compiler libsdl1.2-dev xterm
```
If you encounter an error about `libsdl1.2`, then the following can be used instead:
```bash
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib  build-essential chrpath screen curl device-tree-compiler xterm aptitude
$ sudo aptitude install libsdl1.2-dev
```
### Debian
Install the same packages required for Ubuntu (see above) and then install the following packages.
```bash
$ sudo apt-get install curl gperf quilt
```
### Fedora
```bash
$ sudo yum install gawk make wget tar bzip2 gzip python unzip perl patch diffutils diffstat git cpp gcc gcc-c++ \
    glibc-devel texinfo chrpath ccache perl-Data-Dumper perl-Text-ParseWords perl-Thread-Queue SDL-devel xterm screen dtc
```
### CentOS
OpenSwitch uses the Yocto project. If you are using an old version of CentOS, Yocto will have problems because of the old version of Python included in  the distro.
CentOS ships with Python 2.6.6 and several critical system utilities, for example `yum`, will break if the default Python interpreter is upgraded.
The trick is to install new versions of Python in `/usr/local` (or some other non-standard location) so that they can live side-by-side with the system version.
The following two steps are required for CentOS only:
* Install a pre-built standalone Python for Yocto following these commands:
```bash
cd /tmp
wget http://downloads.yoctoproject.org/releases/yocto/yocto-1.6.1/buildtools/poky-eglibc-x86_64-buildtools-tarball-core2-64-buildtools-nativesdk-standalone-1.6.1.sh
chmod +x poky-eglibc-x86_64-buildtools-tarball-core2-64-buildtools-nativesdk-standalone-1.6.1.sh
sudo ./poky-eglibc-x86_64-buildtools-tarball-core2-64-buildtools-nativesdk-standalone-1.6.1.sh -y
```
* Setup the environment
```bash
source /opt/poky/1.6.1/environment-setup-x86_64-pokysdk-linux
```
