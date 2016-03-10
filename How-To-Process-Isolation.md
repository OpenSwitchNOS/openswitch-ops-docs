# Process Isolation How-To

## Index:

1. [Intro](#intro)
2. [Considerations and Warnings](#considerationswarnings)
3. [Determining root/non-root user](#rootuser)
4. [Step 1: Review System calls](#step1)
5. [Step 2: Review Capabilities](#step2)
6. [Step 3: Review File System Access](#step3)
7. [Step 4: Review Protocols Needed](#step4)
8. [Step 5: Create the profile](#step5)
9. [Step 6: Modify the service file](#step6)
10. [Step 7: Modify recipe](#step7)
11. [Debugging](#debug)
12. [Configuring Docker with BTRFS](#btrfs)
13. [Recompile Kernel with CONFIG_AUFS_XATTR](#aufs)
14. [RESTD prototype Example Experience](#restd)


## Intro <a name="intro"></a>


Process hardening is the act of reducing an executables attack surface.  This means reducing the privilege levels where possible and limiting how the process can access OS resources.  Reducing the privilege level for OPS means changing OPS daemons that currently run with root privileges, to instead run as a normal user, wherever possible.


The second aspect of hardening (or sandboxing) is to limit access to system services by a process to only the minimum set needed to perform its functionality.  There are three main facets to consider;  access to the file system directories and files, allowed system calls, and the set of Linux capabilities needed.  Each of these are covered in detail in the sections to follow.



[Firejail](https://firejail.wordpress.com/) is going to be the tool used to perform “Process Isolation” or “Process Hardening”:



> Firejail is a SUID program that reduces the risk of security breaches by restricting the running environment of untrusted applications using Linux namespaces and seccomp-bpf. It allows a process and all its descendants to have their own private view of the globally shared kernel resources, such as the network stack, process table, mount table.

> Firejail can sandbox any type of processes: servers, graphical applications, and even user login sessions.”



All developers working on hardening their processes must at a minimum read the Firejail and Firejail.profile man pages.  Only summary information is presented in this document.



The following steps will guide you through assessing the system requirements of your process to accurately build a sandbox (via a config definition) that restricts your process to accessing the only resources that it needs.

## Considerations and Warnings: <a name="considerationswarnings"></a>

- Using a non-root user and **combining seccomp filtering and enabling Capabilities** through **setcap** will not work correctly. Seccomp will block the elevated capabilities privileges and the program will not be able to use them. ++It is preferred to enable capabilities and not use seccomp, when running as non-root user++. Root users can combine both.

- To launch a Firejail inside of another sandbox you must use the **-\-force** option. Be aware that **Docker is a sandbox**, you will need this option if you want to try Firejail with Docker.  It is recommended you always supply this option.

- Firejail disables **/etc/firejail** when it runs the sandbox, if you plan to use Firejail to launch a child process inside of an existing Firejail sandbox you have to have the profile in another directory.  This should be extremely rare.

- **System calls** for processes can differ from kernel versions, and from platforms to other platforms. Please note that when whitelisting system calls there is no guarantee that they all have been covered.  Updating any library might add a new system call and the program can crash when it calls it. **Whitelisting should be used carefully.**

- If docker is using the storage driver **AUFS** and the kernel does not have the **CAP_AUFS_XATTR=y** Linux Capabilities can't be set on a file. The workarounds are:
	- Use the **BTRFS** storage driver.
	- Recompile the Kernel with the **CAP_AUFS_XATTR=y** option.

- When testing with Firejail be aware of the **kernel versions** you want to target and what you are testing against.  The set of system calls vary between different kernel versions.  It is recommended to use similar kernel versions.

- For cases where firejail has to be enabled for given user login (as for netop user which schedules /usr/bin/vtysh). You'll need to use a wrapper user_init.sh which is added as the startup script for that user login. For example:

		$ usermod -s $USER_HOME/user_init.sh $USER

		$ cat $USER_HOME/user_init.sh

			- firejail bash

- Firejail has a page for [Known Problems](https://firejail.wordpress.com/support/known-problems/)


## Determining root/non-root user <a name="rootuser"></a>



Currently all OPS daemons run as root. Wherever possible, we want to run the daemons under a non-root user account. It is recommended that each daemon have their own user/group accounts. The primary group should be the same name as the user with supplemental groups added as needed by the daemon. It is proposed that these accounts begin with "ops". For example, for the restd daemon, it would run under the opsrestd user and have a primary group of opsrestd. To add a new user to OPS for *restd*, you need to add the following line to the *yocto/openswitch/meta-distro-openswitch/classes/openswitch-image.bbclass* file:

		useradd -M -U -r -G ovsdb-client -s /bin/false opsrestd; 

Note that restd uses the supplemental group ovsdb-client. To add a new supplemental group, you need to add the following line before the group is used:

		groupadd <group name>;

When to run as root:



- The process requires elevated privileges and assigning a Linux Capability to the process will add more security risks then running the process as root.



- The process requires  access to a privilege file system object (directory or file) and there is no workaround.  For examples, an file system ACL for the user or user's group.



## Step 1: Review system calls: <a name="step1"></a>


The purpose of this step is to derive the set of system calls used by a process so that Firejail can be configured correctly.  Keep in mind that Firejail can accept either a list of allowed system calls (whitelist) or list of calls that are not allowed (blacklist).  For simple OPS daemons, a whitelist is the preferred solution.  A blacklist should only be used for complex programs.  If a non-allowed call is made by the process, it is terminated.  When this occurs in an OPS daemon, you can usually find a stack trace or other error message in the  /var/log/messages file.  This will tell you which system call failed. You can also use `systemctl status` to see any additional errors.



A careful review of the source code should be made to find all system calls made.  However, for Python scripts, it is the interpreter itself that is making the system calls.  In addition, non-Python programs can use libraries that also make system calls indirectly.  To find these indirectly used system calls, you should use the strace method described below.



For each system call found, you must assess if the call requires elevated privileges (e.g. root access).  Then assess if there is a Linux capability that can be set to enable this system call by a non-root user.  This is especially important for determining if a root process can be converted to a non-root user.  The list of system calls with required privileges and associated capabilities allow you to determine if the process can run under a non-root user account.  If a privileged call does not have a corresponding capability, then you must either work around the call, or continue running as root.



To see the full list of system calls that Firejail can allow or block use the command **firejail -\-force -\-debug-syscalls**.  Note this command should be run on the target platform as it is specific to the kernel version used.



Whitelisting system calls can be tricky because knowing the full array of calls the process makes can be cumbersome. The following is a guide that can be used to know which system calls the process makes.



- Run the process with the following command **strace -qcf < process >**



- Run a complete set of tests against the process to be able to execute all the possible path the process makes. This is an iterative process, come up with an initial set and revise it after running feature specific tests.



- When the process exits you will get a list of calls similar to this:



```

% time     seconds  usecs/call     calls    errors syscall



------ ----------- ----------- --------- --------- ----------------

 42.93    3.095527         247     12512           poll

 19.64    1.416000        2975       476           select

 13.65    0.984000        3046       323           nanosleep

 12.09    0.871552         389      2239       330 futex

 11.47    0.827229          77     10680           epoll_wait

  0.08    0.005779          66        88           fadvise64

  0.06    0.004253           4      1043       193 read

  0.06    0.004000           3      1529         3 lstat

  0.00    0.000344           0      2254      1761 stat

[...]

  0.00    0.000000           0         1           fallocate

  0.00    0.000000           0        24           eventfd2

  0.00    0.000000           0         1           inotify_init1

------ ----------- ----------- --------- --------- ----------------

100.00    7.210150                 95061     23256 total

```



++**Note that not all calls can be captured with this command, some might not appear.**++



- Run the process again with the following command **firejail -\-force -\-shell=none --noprofile -\-seccomp.keep=< syscall1 >,< syscall2 >,…,< syscalln > -\- < process >**



- Run the tests again, if the process crashes use `systemctl status <service name>` to determine the failure and then see the log for any additional information (for OPS, syslog is kept in /var/log/messages) or /var/log/audit/auditd.log, depending which is set. A log like the following will appear



```

Apr 6 10:47:01 debian kernel: [12255.533062] audit: type=1326 audit(1428331621.110:4): auid=1000 uid=1000 gid=1000 ses=1 pid=4643 comm=<process> exe="<process path>" sig=31 arch=c000003e syscall=201 compat=0 ip=0x7f9a25feebed code=0x0

```



- The log states the syscall which crashed the process (e.g. **syscall=201**).  Use the following command, **firejail -\-force -\-debug-syscalls | grep 201**, to determine the name of the system call.



- Run the tests again and repeat until all system calls are added to the whitelist



++**When should blacklisting be used instead of whitelisting:**++

- Different arquitecture between platform.

- Kernel Versions change between the different platforms the process is going to be used on.

- Libraries that the process links to change in a regular basis (e.g. internal ops libraries).

- Process is too complex to have it thoroughly tested to verify that all system calls where added to the whitelist.


## Step 2: Review Capabilities: <a name="step2"></a>


When a process runs as **root** it will by default have enabled all **"Linux Capabilities"**.  It is important to block all "Linux Capabilities" an ops-process is not going to use, to prevent unwanted access to a resource in the system. To see the full list of "Linux Capabilities" and their use take a look at this [link.](http://man7.org/linux/man-pages/man7/capabilities.7.html)



 **Note**: if no Linux Capabilities are needed, the process should be running under **non-root** user and be using seccomp filtering.



It is recommended that the process be made to run as a non-root user.  In order to do this, an inventory needs to be made of all the capabilities that will need to be enabled to allow this process to perform its business logic.



To test that the process is running with the correct minimal subset of capabilities we are going to use the **setcap** and **getcap** commands to modify and check the capabilities set on a process. For more information please look at this [link.](https://wiki.archlinux.org/index.php/Capabilities)



++Example:++



- Server556 is a program that will open a socket and bind to port 556. To open any port under 1024 the **CAP_NET_BIND_SERVICE** capability is needed. If this process where to be run by **non-root** a "permission denied" error would occur.



- To add the capability we use the root user and the command setcap

		setcap cap_net_bind_service+eip /path/Server556



- To review which capabilities have been added to a process we use getcap

		getcap /path/Server556



- Once the process has the capability enabled it can be run by a non-root user and it will be able to execute successfully.





Note:

- The list that Firejail will need will normally be the capability with **CAP_** removed. Use command **firejail -\-debug-caps** to see the full list that Firejail supports.



- Firejail can block a capability or allow it, but it cannot add capabilities to a process.





## Step 3: Review file system access: <a name="step3"></a>



Firejail can restrict the process access to the file system, the following options are:

- **blacklist**: Makes the directory or file inaccessible.



- **noblacklist**: Disables blacklist for a directory or file.



- **private**: Gives the process a private copy of the /home/user and /root directory and changes are discarded after the program exists.



- **private-bin**: Creates a new /bin in a temporary filesystem and copies the programs in a given list. The same directory is also bind-mounted over /sbin, /usr/bin and /usr/sbin.



- **private-dev**: Create a new /dev directory. Only dri, null,  full,  zero,  tty, pts, ptmx, random, urandom, log and shm devices are available.



- **private-tmp**: Mounts an empty temporary filesystem on top of /tmp directory.



- **private-etc**: Creates a new /etc in a temporary filesystem and copies all the programs in a given list. All modifications are discarded when the process exists.



- **whitelist**: Allows the access to file and directories that would be used by process. This feature is implemented only for user home, /dev, /media, /opt, /var, and /tmp directories.



- **read-only**: Makes a directory or file read-only.



The goal of this step is to develop an understanding of the directories and files which your process accesses so that you limit access to only those parts of the file system needed.  You should perform a careful review of all of your source code to determine the list of directories and files accessed or created.  Make sure to take into account libraries or python modules used.  With that list, login to the target system (docker container) and note the permissions of the files and directories referenced.  This is needed for two reasons; it will help determine your Firejail isolation strategy, and second if there are permission issues when converting your process to run under a non-root user.  If there are permission issues that cannot be worked around, then you will not be able to run the process under a non-root user.



**Note**: Before deciding to run as a root user, first be sure that it is not possible to modify the files needed to be run as non-root.



## Step 4: Review protocols needed: <a name="step4"></a>



Firejail can block certain protocols used to open sockets. The ones that it can block are **unix, inet, inet6, netlink and packet**. It does it through seccomp checking the first argument made to the socket system call.



To see the list that firejail can support use the command **firejail -\-force -\-debug-protocols**



**Note**: If your process uses the audit log library, you must include netlink.



## Step 5: Create the profile: <a name="step5"></a>



Once the information from the previous steps has been collected the profile can be created.



Create a file called **< process-name >.profile** add this file to the /etc/firejail directory.



Use the following template to create the profile (a '#' can be used as the start of a comment line):



```

#############################################

#Basic Filtering



#Run program directly without user shell

shell none



#Use to join a firejail session to debug

name <process name>



#Enable protocol filter.

protocol <protocol1>,<protocol2>,…,<protocoln>



#############################################

#System Call Filtering

#Choose one of the following (seccomp) and delete the rest



#Default System Calls blacklist, please see [Firjeail Man.](https://firejail.wordpress.com/features-3/man-firejail/ as reference)

seccomp



#Add to default blacklist

seccomp <system call #1>,<system call #2>,…,<system call #n>



#Whitelist System calls

seccomp.keep <system call #1>,<system call #2>,…,<system call #n>



#Blacklist System Calls

seccomp.drop <system call #1>,<system call #2>,…,<system call #n>



#############################################

#Linux Capability Filtering

#Choose one of the following (caps) and delete the rest.

#One of the following options must be in the profile



#Drops all of the Linux capabilities if none are need

caps.drop all



#Blacklist Capabilities

caps.drop <caps #1>,<caps #2>,…,<caps #N>



#Whitelist Capabilities

caps.keep <caps #1>,<caps #2>,…,<caps #N>



#############################################

#Filesystem Filters

#The following are recommended settings, each process owner must see if they apply to their process and modify them as needed



#Keep /bin private and only allow

private-bin bash,ls,cat,sed



#Keep /tmp private

private-tmp



#Keep /dev private

private-dev



#Keep /etc private

private-etc group,hostname,localtime,nsswitch.conf,passwd,resolv.conf



#Set files to Read Only

read-only <file #1>

read-only <file #2>

read-only …

read-only <file #N>



#Blacklist Files

blacklist <file #1>

blacklist <file #2>

blacklist …

blacklist <file #N>



#Whitelist Files

whitelist ${HOME}/ <file #1>

whitelist ~/ <file #2>

whitelist /tmp/ <file #3>

whitelist …

```



**Note**: For more information about profiles please look at [here.](https://firejail.wordpress.com/features-3/man-firejail-profile/)



## Step 6: Modify the service file (OPS daemons only): <a name="step6"></a>



Once the profile is ready you need to modify the service file so systemd will launch the process with Firejail.



For testing purposes the OPS service files are normally located in the /etc/systemd/system/multi-user.target.wants directory on the target system.



In the **`<process-name>`.service** file modify the **ExecStart** to include Firejail.  An example:



		ExecStart=/usr/bin/firejail --user=<non-root> --force -- /usr/bin/restd



The **-\-user** option will tell Firejail to run the process on behalf of the specified user. If you which to run as **root** omit this option.

The **-\-force** option will force launching the Firejail sandbox inside another sandbox (this is required when running inside a docker container and in general should always be supplied).



If the process needs a set of capabilities you will need to add a line. The service file can have multiple "ExecSrartPre" lines.  Each will be executed serially.  **Note:** **"=-"** means ignore failures.



		ExecStartPre=/usr/sbin/setcap cap_<1>,cap_<2>,…,cap_<n>+eip <process with full path>



++**Note:**++

- If the process is a **python script** the capability must be set on the **python interpreter**. You need to use the following command to remove the capabilities after the service has started, so no other script can run with the capabilities:

		ExecStartPost=/usr/sbin/setcap -r <python interpreter with full path>

- if the `<process-name>`.profile file exist in /etc/firejail/ it will automatically be loaded.


- It is recommended that you initially use the "--noprofile" option and verity the daemon is running under the expected user and group.  You can check this with the command:  **ps \-eo uid,gid,args | grep process-name** .


- **Symbolic links** are not supported, use the full link of the process, in the case of python /usr/bin/python is a link to /usr/bin/python2.7, use /usr/bin/python2.7



## Step 7: Modify recipe: <a name="step7"></a>



The **recipe** needs to install the profile to the switch image, the following changes are needed:



- Add Firejail as a dependency in **DEPENDS**



- In the **SRC_URI** add the **file://< process >.profile**



- Add to the function do_install_append (if the function does not exist create it):

		install -d ${D}/etc/firejail/

		install -m 0644 ${WORKDIR}/<process>.profile ${D}/etc/firejail/



- Add to **FILES_${PN}** (add the variables if it does not exist **FILES_${PN} += **)

		/etc/firejail/<process>.profile



## Debugging <a name="debug"></a>



Hints on debugging Firejail:



- Use **-\-** to separate between Firejail arguments and the process



- Add the argument **-\-debug** to have it print additional information, e.g. **firejail < options > -\-debug -\- < process >**



- Use the **-\-noprofile** argument to have firejail skip using a profile when jailing a process.  Essentially no jail is created.



- All the profile options can be added by [command line](https://firejail.wordpress.com/features-3/man-firejail/), making it easier in some occasions to add and remove options, e.g. **firejail -\-noprofile -\-private-bin=bash,ls,cat,sed -\- bash**



- You can know which processes are running under Firejail with **firejail -\-list** and **firejail -\-tree**



- If you have a process running under a jail and the option name was set you can join the same jail with **firejail -\-join=< name >**



- Firejail has a monitoring program which allows to monitor the jails and their behaviours. [firemon](https://firejail.wordpress.com/features-3/man-firemon/) is the programs name.


## Configuring Docker with BTRFS <a name="btrfs"></a>

This section contains the investigation results of what is needed to setup a docker image to use the BTRFS storage driver.  While you might be able to change your local VM to use the BTRFS driver, none of the code changes that depend on this driver (e.g. linux capabilities) can be check in until all developer VMs and all Zuul test VMs are also changed to use the BTRFS driver.

- Install BTRFS Tools to your system. For Ubuntu:

		apt-get install btrfs-tools

- Create a new partition and format it with BTRFS

		mkfs.btrfs -f /dev/sdc1

- Stop Docker

		service docker stop

- Delete and recreate /var/lib/docker

		rm -rf /var/lib/docker; mkdir /var/lib/docker;

- Add to **DOCKER_OPTS** in the Docker configuration file (can be found at /etc/docker or /etc/initd/docker).

		DOCKER_OPTS="-s btrfs"

- Add to **/etc/fstab** the new partition to mount it automatically

		echo  "/dev/sdc1 /var/lib/docker btrfs defaults 0 0" >> /etc/fstab

- Mount the partition

		mount -a

- Start Docker

		service docker start

- You can check that you are using BTRFS storage driver

		docker info

## Recompile Kernel with CONFIG_AUFS_XATTR <a name="aufs"></a>

This section contains the investigation results of what is needed to setup a docker image to use the AUFS and enabled capapilities through setcap.  While you might be able to change your local VM and test your profile, a change that depends one setting capabilities can only be check in when all developer VMs and all Zuul machines have the upgrade.

- Step 1: Download Kernel

		wget https://cdn.kernel.org/pub/linux/kernel/v3.x/linux-3.18.28.tar.xz
		tar -xvf linux-3.18.28.tar.xz


- Step 2: Download AUFS apply patches to the kernel

		git clone http://git.code.sf.net/p/aufs/aufs3-standalone aufs-aufs3-standalone
		cd aufs-aufs3-standalone/
		git checkout origin/aufs3.18.25+

		cp aufs3-base.patch ../linux-3.18.28/
		cp aufs3-kbuild.patch ../linux-3.18.28/
		cp aufs3-mmap.patch ../linux-3.18.28/
		cp aufs3-standalone.patch ../linux-3.18.28/

		cd ../linux-3.18.28

		patch -p1 < aufs3-kbuild.patch
		patch -p1 < aufs3-base.patch
		patch -p1 < aufs3-mmap.patch
		patch -p1 < aufs3-standalone.patch

		cd ../aufs-aufs3-standalone/

		cp -r ./Documentation/* ./linux-3.18.28/Documentation/
		cp -r ./fs/* ./linux-3.18.28/fs/
		cp ./include/uapi/linux/aufs_type.h ./linux-3.18.28/include/uapi/linux/

- Step 3: Set Kernel Configuration

	1. Set your Kernel Configuration Settings
	2. Enable under File systems -> Miscellaneous filesystems, XATTR for AUFS
	3. Check that you have everything aufs needs, use the [script](https://raw.githubusercontent.com/docker/docker/master/contrib/check-config.sh)
		`./check-config <your-config>`
	4. Check for **CONFIG_AUFS_XATTR=y**

- Step 4: Compile and Install Kernel

		sudo make
		sudo make modules_install
		sudo make headers_install
		sudo make install

		sudo grub-set-default "Ubuntu, with Linux 3.18.28"
		sudo update-grub

- Setp 5: Reboot

## RESTD prototype Example Experience <a name="restd"></a>

The OPS daemon restd was chosen as the initial example process to harden.  One of the principles that should be adopted is to start from a known working state and make small incremental changes.  That allows you to determine which change causes a failure.  Failures will be a common event as you begin to harden a process.  Although tedious, it did seem to be the most productive approach.

Two engineers were working simultaneously on this prototype.  One tackled getting restd to startup as a non-root user and the other investigated using Firejail with a profile.  We will first look at running restd as a normal user.

### Getting restd to run under the opsd user account

- The first step was to log in to a docker image and edit the restd service file to add a "User" and "Group" statements to set **opsd** as the user and **ovsdb-client**" as the group respectively.

- Then a **daemon-reload** command was issued.  As expected the restd service failed to start.  Looking at the /var/log/messages file showed that restd received a "Permission denied" error when trying to create the /var/run/persistent_cookie_secret file. **Note:** `systemctl reload` needs to run everytime the `.service` file is changed.

- Looking at the permissions for /var/run/ which is a link to the /run directory shows that it is only writable by root.  Reverse engineering the source code shows that the cookiesecret.py file in the ops-aaa-utils repo creates the file.

Here is a case of a file operation requiring root privileges that required a work around.  To work around this issue, the following changes needed to be made.  Modify the cookiesecret.py file to create the file in the /var/run/aaa/ directory instead of the /var/run/ directory.  Modify the aaautils.service file and add the following two lines:

	ExecStartPre=/bin/mkdir -p -m 0700 /var/run/aaa

	ExecStartPre=/bin/chown opsd:ovsdb-client /var/run/aaa

These changes were made, the restd service started, and the next failure was observed.  The messages file indicated a failure in the bind call.  The stack trace showed restd trying to use a port number of 443 to create the HTTPS listen socket.  This of course requires root access.  A change was made in the restd settings.py file to use port 4433.  At his point restd was able to be started under the opsd user account.

### Getting restd to pass gate test with a firejail profile running as root

The approach used was to create a first pass complete firejail profile for restd.  The restd service file was then modified to use that profile with restd running as root.  To validate these changes the complete set of gate (tier 1) rest tests were run.  This generated 11 test failures.  The service file change to use firejail was as follows:

	ExecStart=/usr/bin/firejail --force --profile=/etc/firejail/restd.profile -- /usr/bin/restd

This was a big bang approach which is not recommended.  Instead, it is recommended that you analyze what profile settings are most likely to work without problems and then incrementally add new items to the profile and running all tests (tier 1 & 2) against each change.  Instead, we continued to remove profile items, rerunning the tests with each change until all tests passed.  At that point you know which items were causing the failures.  The other items could be re-added and the gate tests rerun to verify the tests still passed.

### Combining running with a firejail profile as a non-root user

The final step was to combine all of the changes made in getting restd to run under the **opsd** user account with a firejail profile.  We remove the "User" and "Group" statements from the restd service file and changed the "ExecStart" statement as follows:

	ExecStart=/usr/bin/firejail --force --user=opsd --profile=/etc/firejail/restd.profile -- /usr/bin/restd

All of the gate tests resulted in 80 failures.  Analysis showed that all of the rest gate tests have hard coded the use of port 443.  These test were modified to use port 4433 to match the change made to restd.  The tests were rerun resulting in 11 failures.  Analysis showed that the test framework RestCmd() method used to issue a rest call from a workstation to a switch has also hard coded the use of port 443.  There is no way to override the port used or ability to change the test framework.

Given this fact, all port changes were restored to use 443.  The work around is to use the Linux CAP_NET_BIND_SERVICE capability on the restd process to allow restd to bind to the privileged port.
