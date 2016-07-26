# How to debug

## Contents

- [Overview](#overview)
- [Debugging code](#debugging-code)
    - [Prerequisites for debugging](#prerequisites-for-debugging)
    - [On-target debugging](#on-target-debugging)
        - [Setting up on-target debug](#setting-up-on-target-debug)
        - [Using on-target debug](#using-on-target-debug)
    - [Off-target debugging](#off-target-debugging)
- [Debugging periodic builds](#debugging-periodic-builds)
- [Analysing coredumps](#analysing-coredumps)
    - [Coredumps on target](#coredumps-on-target)
    - [Coredumps on periodic images](#coredumps-on-periodic-images)
- [Using Eclipse as an IDE](#using-eclipse-as-an-ide)
    - [Installing Eclipse and dependencies](#installing-eclipse-and-dependencies)
    - [Configuring Eclipse](#configuring-eclipse)

## Overview
OpenSwitch is built on top of the Yocto Project, and offers extensive functionality for debugging. This page provides information about how these debugging features are integrated with OpenSwitch.

## Debugging code

There are two main ways to debug code:

- **On-target debugging**: Run the debugger on the target hardware and have a local copy of the symbols and source code.

- **Off-target debugging**: Run the debugger on a developer's machine and connect remotely to a debugging agent on the target hardware. The symbols and source code refer to locations on the developer's machine.

Each of these methods has its own advantages and disadvantages, and both are supported.

### Prerequisites for debugging

Access to the debug symbols and the original source code used to compile the code is required.

Typically debug symbols are included on the binaries using the "-g" flag on gcc. However, the details vary depending on the configuration system being used. For example, on cmake is controlled with the CMAKE_BUILD_TYPE configuration variable, but for autotools the flag is added manually.

Furthermore, Yocto usually strips and extracts the debug symbols when building the packages of the final file system image, and places the debug symbols and relevant source files in a separate debug package.

For most libraries/programs in OpenSwitch that are developed with cmake, if the recipe inherits the openswitch class, then all the setup is automatically completed. cmake is invoked in debug mode, and the packages are split to create the debug information. If the application is not based on cmake or inherited from the openswitch class, these details must be completed manually.

### On-target debugging

#### Setting up on-target debug

In order to perform on-target debugging, the debug packages needs to be included in the final image. To do this, modify the local configuration with the following command from the top of the build directory:

```
echo 'EXTRA_IMAGE_FEATURES="dbg-pkgs"' >> build/conf/local.conf

```

After adding the content to the `local.conf` file, build a new image and deploy it to the target hardware. Be aware that the size of the image may increase noticeably due to the inclusion of the source code.

#### Using on-target debug

For every application and library compiled with debug symbols, a `.debug` file is stored in the `/usr/lib/debug` directory, and the corresponding source files at `/usr/src/debug`. For example, to debug the `/usr/bin/vland` daemon, the symbol file is located at `/usr/lib/debug/usr/bin/vland.debug`, and the file paths point to the right location in the `/usr/src/debug` directory. For debugging daemons built using autotools, the debug symbol files are in `/usr/lib/debug/usr/sbin/`.

For example, to debug the running vland instance, connect to the process with the following commands on the target:

```
$ ps aux | grep vland # to find the PID of the running daemon, let's say was 266

$ gdb

(gdb) file /usr/lib/debug/usr/bin/vland.debug

(gdb) attach 266

```

### Off-target debugging

Yocto provides integration with Eclipse as IDE for development and debugging of code. This makes debugging more effective. It is necessary to check the following steps before starting:

1. Install Eclipse and all the components that the environment needs. For more information, see [Working using Eclipse as IDE](#using-eclipse-as-an-ide).

1. Set up the developer environment.  Make sure that the environment meets the [Prerequisites](prerequisites), and follow the [Step by Step Guide](step-by-step-guide).

1. Run the TCF Agent on the device to connect to it by telnet, and run the `systemctl start tcf-agent` command. The TCF agent is  run over port 1534. If it is necessary to check the agent, run `netstat -nl` to see the following:

```

Active Internet connections (only servers)

Proto Recv-Q Send-Q Local Address           Foreign Address         State

tcp        0      0 0.0.0.0:5355            0.0.0.0:*               LISTEN

tcp        0      0 0.0.0.0:1943            0.0.0.0:*               LISTEN

tcp        0      0 0.0.0.0:1534            0.0.0.0:*               LISTEN

tcp        0      0 :::5355                 :::*                    LISTEN

tcp        0      0 :::22                   :::*                    LISTEN

udp        0      0 0.0.0.0:5355            0.0.0.0:*

udp        0      0 0.0.0.0:1534            0.0.0.0:*

```

The TCF-Agent is in charge of the communication between the device and Eclipse. It broadcasts in auto discovery mode.

The following steps creates the project in Eclipse based on a Yocto template:

1. Open Eclipse.

2. Select File -> Import -> General - > Existing Projects into WorkSpace, and then click Next.

To import projects:

1.   Browse to the root directory and look for the code. The code is similar to the following: `directory/src/package_name`.

2.   Click Finish. The daemon that you are working with is displayed in the Project Explorer.

Select the project:

1.  Select Go to Project-> Clean

2. Select Go to Project-> Build All

The second method of off-target debugging is through gdb. If a `make devenv_add` has been completed for the repos, they are ready for debugging.

1. Find the IP address of the target using the `ifconfig` command.
   ```
   bash-4.3# ifconfig
   eth0		 Link encap:Ethernet  HWaddr 02:42:AC:11:00:25
           inet addr:172.17.0.2  Bcast:0.0.0.0  Mask:255.255.0.0

   Here my ip is 172.17.0.2
   ```

2. Find the Process ID(PID) for the daemon to be debugged, and launch a gdb-server on the target.
   ```
   gdbserver <IP of Target>:<Free port> --atach <pid> &
   ```
   For example:
   ```
   bash-4.3# gdbserver 172.17.0.2:5959  --attach `pidof ops-sysd` &
   [1] 17502
   bash-4.3# Attached; pid = 293
   Listening on port 5959
   ```

3. From "ops-build" directory on your VM, start gdb with the debuggable binary.
```
gdb build/tmp/work/core2-64-openswitch-linux/ops-sysd/git999-r0/image/usr/bin/ops-sysd
```

4. From the gdb prompt on the VM, connect to the gdbserver.
```
gdb> target remote <IP>:<Port>
For example:
gdb> target remote 172.17.0.2:5959
```
5. Set sysroot to pick up the debug symbols.
```
gdb> set sysroot /ws/madhavab/master_05_04/ops-build/build/tmp/sysroots/genericx86-64
gdb> bt
 #0  0x00007fc9a90ecbb0 in __poll_nocancel () at ../sysdeps/unix/syscall-template.S:81
 #1  0x00007fc9aa11713b in time_poll (pollfds=pollfds@entry=0x1595ab0, n_pollfds=3, handles=handles@entry=0x0, timeout_when=9223372036854775807, elapsed=elapsed@entry=0x7ffda642114c)
    at /mnt/jenkins/workspace/ops-periodic-genericx86-64/build/tmp/work/core2-64-openswitch-linux/ops-openvswitch/gitAUTOINC+a01fdf45b7-r0/git/lib/timeval.c:302
 #2  0x00007fc9aa10cddc in poll_block () at /mnt/jenkins/workspace/ops-periodic-genericx86-64/build/tmp/work/core2-64-openswitch-linux/ops-openvswitch/gitAUTOINC+a01fdf45b7-r0/git/lib/poll-loop.c:377
 #3  0x0000000000407054 in main (argc=<optimized out>, argv=<optimized out>) at /usr/src/debug/ops-sysd/git999-r0/src/sysd.c:503
```

## Debugging periodic builds
In order to make debugging easier, debugging symbols are part of periodic builds and available as a tarball. The developer does not need to build a separate image with debugging symbols since the periodic image is debug-ready.

Download periodic images from https://archive.openswitch.net/artifacts/periodic/.

For example, to debug the latest genericx86-64 build from the master branch:
1. Go to https://archive.openswitch.net/artifacts/periodic/master/latest/genericx86-64/.
2. Download two .tar.gz files. The tarballs are named similar to:
   - openswitch-disk-image-genericx86-64.dbg-ops-0.4.0-master+2016051618.tar.gz - The tarball with "dbg" as part of its name contains the debug symbols.
   - openswitch-disk-image-genericx86-64-ops-0.4.0-master+2016051618.tar.gz - The tarball without "dbg" as part of its name is the Openswitch image.
3. Untar the downloaded tarballs into a single folder.
```
   debug_ops$ tar -xvzf openswitch-disk-image-genericx86-64.dbg-ops-0.4.0-master+2016051618.tar.gz
   debug_ops$ tar -xvzf openswitch-disk-image-genericx86-64-ops-0.4.0-master+2016051618.tar.gz
```

1. Deploy the same Openswitch image on the target. Find the Process ID(PID) for the daemon to be debugged, and launch a gdb-server on the target.
```
gdbserver <IP of Target>:<Free port> --atach <pid> &
```
For example:
```
   bash-4.3# gdbserver 172.17.0.2:5959  --attach `pidof ops-sysd` &
   [1] 17502
   bash-4.3# Attached; pid = 293
   Listening on port 5959
```

Remote debug from the host where the debug symbols and periodic image is extracted.
1. Start gdb from the directory that constains the debug symbols and periodic image. This launches the gdb prompt.
```
   debug_ops$ sudo gdb
```

2. From the gdb prompt, connect to the gdbserver started on the target.
```
   gdb>target remote 172.17.0.2:5959
```
   On the target, `Remote debugging from host 172.17.0.1` is displayed.

3. Set the path to the debug symbols of the daemon to be debugged. In the case of ops-sysd:
```
   gdb>file /ws/madhavab/debug_ops/usr/lib/debug/usr/bin/ops-sysd.debug
```

4. Set the path for the libraries.
```
   gdb>set solib-search-path /ws/madhavab/debug_ops
```

5. Now gdb has the necessary debug symbols, and can issue gdb commands such as backtrace.
```
   gdb>bt
   #0  0x00007f0195537bb0 in __poll_nocancel () at ../sysdeps/unix/syscall-template.S:81
   #1  0x00007f019656313b in time_poll () from /ws/madhavab/debug_ops/usr/lib/libovscommon.so.1.0.0
   #2  0x00007f0196558ddc in poll_block () from /ws/madhavab/debug_ops/usr/lib/libovscommon.so.1.0.0
   #3  0x00000000004067a2 in main (argc=<optimized out>, argv=<optimized out>)
    at /usr/src/debug/ops-sysd/gitAUTOINC+6a5b7684f3-r0/git/src/sysd.c:503
```

## Analysing coredumps
Coredumps are stored in the /var/diagnostics/coredump directory in .xz format.

### Coredumps on target
If the image loaded on the target is pre-built with debug symbols, use the `coredumpctl` command to analyze the coredump.

```
root@switch:/var/diagnostics/coredump# ls
core.vtysh.0.515c4d7ce7184c578e5f67ae20d3e6e5.928.1461694120000000.xz
processed_core_files.cfl

root@switch: coredumpctl gdb
```
This command invokes the gnu debugger on the last coredump matching specified characteristics. Run "bt"(backtrace) on the gdb prompt to analyze the coredump.

Please note that coredumpctl has a limitation to debug coredumps from the previous boot.
For debugging coredumps from a previous boot, use the "show core-dump" command to list the available core dumps.  Also use the "copy core-dump" command to copy the coredumps out and perform off-target debugging.

### Coredumps on periodic images

To analyze a coredump generated from a periodic image, leverage the debug symbols which are part of the periodic builds.
1. Download the coredump file and extract it.
2. Download the tar ball of the periodic image and untar it.
3. Download the tar ball of the debug symbols in the periodic image and untar it.
4. Place the contents obtained from step 1-3 in a single folder.
5. From this folder, launch gdb specifying the binary and the coredump file. For example:
```
analyse_dump$ sudo gdb usr/lib/debug/usr/bin/vtysh.debug --core=core.vtysh.0.96a458a6a3f8417d9f232bcffdf9ef55.2540.1461695410000000
```
6. Set the search path for loading non-absolute shared library symbol files.
```
gdb> set solib-search-path /ws/madhavab/analyse_dump
```
7. Backtrace to analyze the coredump.
```
gdb> bt

 #0  0x00007f2bec73b11a in cli_system_get_psu () from /ws/madhavab/coredump/usr/lib/cli/plugins/libpower_cli.so
 #1  0x00007f2bf0efa4b4 in ?? () from /ws/madhavab/coredump/usr/lib/libops-cli.so.0
 #2  0x00007f2bf0efa710 in cmd_execute_command () from /ws/madhavab/coredump/usr/lib/libops-cli.so.0
 #3  0x000000000041ce0f in vtysh_execute_func (line=0xc6bf00 "show system power-supply ", pager=<error reading variable: can't compute CFA for this frame>)
    at /usr/src/debug/ops-cli/gitAUTOINC+57ad6ccc8d-r0/git/vtysh/vtysh.c:403
```

## Using Eclipse as an IDE

Since OpenSwitch is built using Yocto, it provides integration with Eclipse as an IDE for development and debugging code. To work with Eclipse a recent version of Eclipse and certain plugins needs to be installed.

### Installing Eclipse and dependencies

This step may be skipped if a Vagrant-based OpenSwitch machine is being used for development, since it ships with Eclipse installed in the /opt/eclipse directory.

```
$ wget http://ftp.osuosl.org/pub/eclipse//technology/epp/downloads/release/luna/SR2/eclipse-cpp-luna-SR2-linux-gtk-x86_64.tar.gz

$ sudo tar xzf eclipse-cpp-luna-SR2-linux-gtk-x86_64.tar.gz -C /opt/

$ sudo -E /opt/eclipse/eclipse -application org.eclipse.equinox.p2.director -noSplash \

-repository http://download.eclipse.org/releases/luna -installIUs

org.eclipse.egit.feature.group,org.eclipse.jgit.feature.group,org.eclipse.mylyn_feature.feature.group,\

org.eclipse.linuxtools.gcov.feature.group,org.eclipse.linuxtools.cdt.libhover.devhelp.feature.feature.group,\

org.eclipse.linuxtools.lttng2.control.feature.group,org.eclipse.linuxtools.lttng2.ust.feature.group,\

org.eclipse.linuxtools.lttng2.kernel.feature.group,org.eclipse.linuxtools.oprofile.feature.feature.group,\

org.eclipse.linuxtools.perf.feature.feature.group,org.eclipse.linuxtools.systemtap.feature.group,\

org.eclipse.linuxtools.valgrind.feature.group,org.eclipse.tcf.te.tcf.feature.feature.group,\

org.eclipse.tcf.te.terminals.feature.feature.group,org.eclipse.tcf.te.tcf.launch.cdt.feature.feature.group,\

org.eclipse.tcf.rse.feature.feature.group,org.eclipse.tcf.cdt.feature.feature.group,\

org.eclipse.cdt.build.crossgcc.feature.group,org.eclipse.cdt.debug.ui.memory.feature.group,\

org.eclipse.cdt.autotools.feature.group,org.eclipse.linuxtools.callgraph.feature.feature.group,\

org.eclipse.linuxtools.cdt.libhover.feature.feature.group,org.eclipse.wst.xml_ui.feature.feature.group

$ sudo -E /opt/eclipse/eclipse -application org.eclipse.equinox.p2.director -noSplash \

-repository http://downloads.yoctoproject.org/releases/eclipse-plugin/1.8/luna/ \

-installIUs org.yocto.sdk.feature.group,org.yocto.doc.feature.group

```

### Configuring Eclipse

1. Ensure the proxy settings are properly set up in Window -> Preferences -> General -> Network connection. Otherwise, if you are running over x2go, Eclipse does not inherit the proxy settings from the console environment.

1. Create a profile to work with the OpenSwitch environment in Window -> Preferences -> Yocto Project ADT.

1. In Cross Compiler Options select 'Build system derivated toolchain'. Enter the toolchain root location, sysroot location, target architecture, and target options.
    - Toolchain Root Location: `</path/to/openSwitch/directory>/build`
    - Sysroot Location: `</path/to/openSwitch/directory>/build/tmp/sysroots/<machine name>`
    - Target Architecture: `auto-select`
    - Target Options: External HW
