# How to debug

OpenSwitch is built on top of the Yocto Project and offers extensive functionality for debugging. This page provides guidance regarding how these debugging features are integrated with OpenSwitch.

## Contents

- [Debugging code](#debugging-code)
	- [Pre-requisites for debugging](#pre-requisites-for-debugging)
	- [On-target debugging](#on-target-debugging)
		- [Setting up on-target debug](#setting-up-on-target-debug)
		- [Using on-target debug](#using-on-target-debug)
	- [Off-target debugging](#off-target-debugging)
- [Instrumenting code](#instrumenting-code)
- [Tracing code](#tracing-code)
- [Working using Eclipse as IDE](#working-using-eclipse-as-ide)
	- [Installing Eclipse and dependencies](#installing-eclipse-and-dependencies)
	- [Configuring Eclipse](#configuring-eclipse)


## Debugging code

In order to debug code, there are two main ways to debug:
- **On-target debugging**: run the debugger on the target hardware and have a local copy of the symbols and source code.
- **Off-target debugging**: run the debugger on the developer machine and attach remotely to a debugging agent on the target hardware. The symbols and source code refer to locations on the developer's machine.

Each of these methods have its own advantages/disadvantages, therefore we support both and is up to the developer to use the one he feels more comfortable with.

### Pre-requisites for debugging

In order to debug code you need access to the debug symbols and the original source code used to compile the code.

Typically debug symbols are included on the binaries using the "-g" flag on gcc (however the details vary depending on the configuration system of your choice). For example on cmake, that is controlled with the CMAKE_BUILD_TYPE configuration variable, where in autotools you need to add the flag manually.

Further more, Yocto usually strips and extracts the debug symbols when building the packages of the final file system image, usually placing the debug symbols and relevant source files in a separate debug package.

For most libraries/programs in OpenSwitch that are developed with cmake, if the recipe inherits the openswitch class, then all this setup is taken care: cmake is invoked in debug mode, and the packages are split to create the debug information. If your application is not based on cmake or inherit form this class, you will have to take care of these details yourself.

### On-target debugging

#### Setting up on-target debug

In order to perform on-target debugging, you need to include the debug packages in your final image. To do this, modify your local configuration with the following command from the top of your build directory:

``` bash
echo 'EXTRA_IMAGE_FEATURES="dbg-pkgs"' >> build/conf/local.conf
```

After adding that content to the `local.conf` file, build a new image and deploy it to the target hardware. Be aware that the size of the image may increase noticeably due the inclusion of the source code.

#### Using on-target debug

For every application and library that includes debug symbols (meaning that was properly compiled with them), you will find a `.debug` file in the `/usr/lib/debug` directory, and the corresponding source file at `/usr/src/debug`. For example if you want to debug the `/usr/bin/vland` daemon, the symbol file will be located at `/usr/lib/debug/usr/bin/vland.debug`, and their file paths will be pointing to the right location at the `/usr/src/debug` directory. For debugging daemons built using autotools the debug symbol files will be in `/usr/lib/debug/usr/sbin/`

For example to debug the running vland instance, you could attach to the process with the following commands on the target:

```bash
$ ps aux | grep vland # to find the PID of the running daemon, let's say was 266
$ gdb
(gdb) file /usr/lib/debug/usr/bin/vland.debug
(gdb) attach 266
```

### Off-target debugging

Yocto provides integration with Eclipse as IDE for development and debugging of code, this features is going to help us to do a better debug in our project. Before you start in this section is necessary to check the following steps:

- It is necessary to have installed Eclipse and all the components that the environment needs. You can check them in the section [Working using Eclipse as IDE](#working-using-eclipse-as-ide)
- Also is necessary the developer environment, if you have question about this point you can check the following link [Managing the Development Environment](#./development-environment.md)
- The last step is run TCF Agent into de device, that way you can connect to the device by telnet and run de following command `systemctl start tcf-agent` the TCF agent is going to run over the port 1534. If is necessary to check the agent, you can run `netstat -nl` and you will see the following result

``` bash
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

**TCF-Agent** is in charge of the communication between the device and the eclipse. This agent sends broadcast in auto discovery mode.

Now we have to create the project in Eclipse based on a Yocto template.

- Open Eclipse.
- Select File -> Import -> General - > Existing Projects into WorkSpace.
  - Press Next
- Import Projects:
  - Browse in Select root directory and look for you code. It has to be something similar to `directory/src/package_name`
  - Press Finish
In you Project Explorer you will see the daemon that you are working with.

- Select the Project
  - Go to Project-> Clean
  - Go to Project-> Build All

## Working using Eclipse as IDE

Since OpenSwitch is build using Yocto, it provides integration with Eclipse as IDE for development and debugging of code. To work with it you need to install a recent version of Eclipse and certain plugins that guarantee an smooth experience with it.

### Installing Eclipse and dependencies

You can skip this step if you are using a Vagrant-based OpenSwitch machine for development, as it already ships with a properly setup eclipse installed on /opt/eclipse directory.

``` bash
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

- If you are running over x2go, eclipse won't inherit the proxy settings from your console environment, so be sure to properly setup your proxy settings in Window -> Preferences -> General -> Network connection.
- Create a profile to work with your OpenSwitch environment in Window -> Preferences -> Yocto Project ADT:
  - In Cross Compiler Options select 'Build system derivated toolchain'
    - Toolchain Root Location: `</path/to/openSwitch/directory>/build`
    - Sysroot Location: `</path/to/openSwitch/directory>/build/tmp/sysroots/<machine name>`
    - Target Architecture: `auto-select`
  - Target Options:
    - External HW
