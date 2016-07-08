# Changing OpenSwitch Code

##Contents

- [Overview](#overview)
- [Prerequisites for using the development environment](#prerequisites-for-using-the-development-environment)
- [Managing the development environment](#managing-the-development-environment)
	- [Workflows](#workflows)
		- [Working with one repository](#working-with-one-repository)
		- [Component Testing](#component-testing)
		- [A note on the new testing framework](#a-note-on-the-new-testing-framework)
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
	- [Testing commands](#testing-commands)
		- [testenv_init](#testenv_init)
		- [testenv_run](#testenv_run)
		- [testenv_rerun](#testenv_rerun)
		- [testenv_suite_list](#testenv_suite_list)
		- [testenv_suite_run](#testenv_suite_run)
		- [testenv_suite_rerun](#testenv_suite_rerun)
	- [Coverage commands](#coverage-commands)
        - [Generate coverage report for C-based code](#Generate%20coverage%20report%20for%20C-based%20code)
        - [Generate coverage report for Python-based code](Generate%20coverage%20report%20for%20Python-based%20code)
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
Component tests are run using `pytest` and the `topology modular framework`.
* Set up the sandbox for running tests:
```
make testenv_init
```
This will install all necessary components and verifies the system is ready to run tests
**Note**: While performing this you may be prompted to input your sudo password this is a one time only requirement.

* Creating a build and running a testsuite against a particular component:
```
make testenv_run feature ops-cli
```
**Note**: This command will always build an image.

* To run without generating a new build:
```
make testenv_rerun feature ops-cli
```

#### A note on the new testing framework

* There are two frameworks currently in place, legacy and modular
* To run legacy test cases use 'legacy' as the testsuite to run:
```
make testenv_rerun legacy ops-cli
```

The build system will recognize the legacy test suite and will invoke the legacy framework.
**Note**: Legacy test cases are looked for in the 'test/' directory, while modular framework test cases must live in the 'ops-tests/' directory

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
### Testing commands
This section provides a complete list of all the testing commands that can be used when contributing code to the OpenSwitch project.

#### testenv_init
Initiates the testing environment.
```
$ make testenv_init
```
This will verify the system is ready to run tests
**Note**: While performing this you may be prompted to input your sudo password this is a one time only requirement.

#### testenv_run
This command can be used to run a test in legacy as well as the modular framework. It builds an image and runs a test suite against one or multiple components.
```
$ make testenv_run <type> <module> <optional-parameters>
```
where,
**type** = feature (run feature tests from modular framework, `ops-tests/feature`), component (run component tests from modular framework, `ops-tests/component`) or legacy (run feature or component tests from legacy framework, `< module-name >/tests`)
**module** = repository for which the tests are to be run
**optional parameters** = If these parameters are not provided, entire test suit for the selected types and modules will run. The optional parameters can be `TESTENV_ABORT_IF_NOT_FOUND`, `TESTENV_STRESS` and `TESTENV_ITERATIONS`.
`TESTENV_STRESS` parameter will execute one test case instead of all the test cases under the directory.
Using the optional `TESTENV_ITERATIONS` in conjunction with `TESTENV_STRESS` will allow you to indicate the number of times the test will be executed.
Otherwise the `TESTENV_STRESS` will execute the test a random number (between 3 and 15) of times.
`TESTENV_ABORT_IF_NOT_FOUND` parameter will skip repositories without test (component/feature) in a list.

**Examples**:
```
$ make testenv_run feature ops-cli
```
This will run all the feature tests written in modular-framework (`ops-cli/ops-tests/feature`) for ops-cli module.
**Note**: This command will always build the image again. If you don't require this please look at the `testenv_rerun` command.

```
$ make testenv_run component ops-cli
```
This command will run all the component tests written in modular-framework (`ops-cli/ops-tests/component`) for ops-cli module.

```
$ make testenv_run legacy ops-cli
```
This command will run all the component tests written in legacy-framework (`ops-cli/tests/`) for ops-cli module.

```
$ make testenv_run legacy ops
```
Since for the legacy framework all the tests are under ops/tests, this command will run all the feature tests written in legacy-framework (`ops/tests/`).

```
$ make testenv_run component ops-arpmgrd TESTENV_STRESS=test_arpmgrd_ct_transaction_failure TESTENV_ITERATIONS=25
```
This command will build the image and only run the **test_arpmgrd_ct_transaction** component test written in modular framework from ops-arpmgrd module 25 times.
**NOTE**:This command is very useful to stress the tests and should be used by all developers locally; before uploading any test for review

```
$ testenv_run component ops-aaa-utils ops-fand TESTENV_ABORT_IF_NOT_FOUND=false
```
This command will execute all component tests written in the modular test framework (tests under <module-name>/ops-tests/component/). If TESTENV_ABORT_IF_NOT_FOUND is set to False this execution will skip repositories that does not have ops-tests/ directory and continue to the next repository. The default value is TRUE and execution will stop if the ops-tests/ directory does not exist. The same parameter can be used for feature tests too.

#### testenv_rerun
Runs a provided test suite against one or multiple components without building a new image. The only difference between testenv_run and this command is that, this one does not build the image. Everything else is the same and thus, all the examples shown above can be used with this command in the exact same manner.
Developers should use testenv_run once and testenv_rerun should be used for subsequent runs.

**One example for reference**
```
$ make testenv_rerun feature ops-cli
```
The above command will look for test cases under the `ops-cli/ops-tests/feature` directory.
**Note**: This command will not rebuild an image. If you need to build an image with your changes please look at the `run` command.

The optional parameters `TESTENV_STRESS` and `TESTENV_ITERATIONS` too works in the exact same way as for the testenv_run command.
```
$ make testenv_rerun component ops-arpmgrd TESTENV_STRESS=test_arpmgrd_ct_transaction_failure  TESTENV_ITERATIONS=25
```
This command will only run the **test_arpmgrd_ct_transaction** component test written in modular framework from ops-arpmgrd module 25 times.
**NOTE**: This command is very useful to stress the tests and should be used by all developers locally; before uploading any test for review


#### testenv_suite_list
Provides a list of available test suites on all Yocto layers.
```
$ make testenv_suite_list
```

#### testenv_suite_list_component
Provides a list of available test suites for a specific component.
```
$ make testenv_suite_list_components ops-cli
```

#### testenv_suite_run
Builds an image and runs a test suite against multiple components.
```
$ make testenv_suite_run feature
```
**Note**: This command will look for a testsuite_<testsuite>.conf file in each component (i.e. ops-cli/testsuite_feature.conf)

#### testenv_suite_rerun
Runs a test suite against multiple components.
```
$ make testenv_suite_rerun feature
```
**Note**: This command will look for a testsuite_<testsuite>.conf file in each component (i.e. ops-cli/testsuite_feature.conf)

### Coverage commands
The coverage command creates a coverage report for the Feature and Component tests (FT/CTs).

#### Generate coverage report for C-based code

##### Requirements
The `generate_coverage_report` is a make target that exists for all repos that have been added to the devenv. This make target uses the `testenv_run` target internally to run the FT/CTs that produce the coverage data. Therefore is is required to initialize the testenv prior to running the coverage target, i.e.:
```
make testenv_init
```

Additionally, the `generate_coverare_report` target uses the [LCOV](http://ltp.sourceforge.net/coverage/lcov.php) tool to produce the HTML coverage report. Make sure to install LCOV on your host before invoking the coverage target, e.g.:
```
sudo apt-get install lcov
```

##### Running the coverage report
The `generate_coverage_report` make target is available for repos that have been added to the devenv and can be invoked by appending `-generate_coverage_report` to the recipe name, e.g.:
```
$ make ops-myrepo-generate_coverage_report
```

##### Expected results
The `generate_coverage_report` command will perform the following tasks:
1. Compile your repo with coverage flags
1. Create a new Docker image
1. Export the image to Docker
1. Run the FT/CTs
1. Capture the coverage data from the Docker containers
1. Create an HTML report at `coverage/html/index.html`
1. Output a summary to the stdout, e.g:

```
**** Configuration and result info: ****

The configured coverage threshold is: 80
The configured FT/CT code coverage exclude pattern is: *openvswitch* *CMake* *ovs/*
The actual reported coverage is:   58
The location of the FT/CT code coverage report is: coverage/html
```

##### Configuration
The `generate_coverage_report` checks for the existence of a file named `ft_ct_coverage` at the root of the repo being tested. If this file does not exist, the tests are not run and the report is not produced.

The configuration file `ft_ct_coverage` accepts the following two options:
1. Minimum_coverage
1. Exclude_pattern

If the file exists but is empty, the coverage will still run with a default value of -1 for `Minimun_coverage` and no value for `Exclude_pattern`.

Refer to the example file below which documents the options:
```
# Specify the minimum coverage percentage to be accepted for this repo in the following form
#Minimum_coverage=80
Minimum_coverage=65
# Specify the code coverage exclude patterns (if any) using wild-cards and separated by spaces
# Example, to exclude files and directories that contain gtest or openvswitch use the following
# Exclude_pattern=*gtest* *openvswitch*
Exclude_pattern=*openvswitch* *CMake* *ovs/*
```

##### Limitations
This first implementation only supports virtualized environments (with Docker containers) and C/C++ code.
Coverage for CLI (plugins) is not getting generated. This is being investigated.

##### Production code requirements
In order to produce the coverage data, the binary is compiled with [gcov](https://gcc.gnu.org/onlinedocs/gcc/Gcov.html) coverage flags. This tool keeps the coverage data in memory and dumps it to disk on process exit. Therefore, it is required that the process is able to exit cleanly (exit(0) or return from main) for the coverage data to be captured.

##### Test code requirements
The process under test writes the coverage data to disk on exit, therefore it is required that after the run of each test the process is instructed to exit. The way that this can be achieved varies depending on the binary and the test. In general for OpenSwitch daemons that are started as SystemD services, the following can be executed during the test tear-down:
```
systemctl stop ops-myrepo
```

##### Troubleshooting
```
WARNING: No coverage notes file was generated during module compilation. Exiting now with no coverage report
```

Suggested steps:
1. Make sure that the module under test is C/C++
1. Try removing and re-adding the repo to the devenv (`make devenv_[rm|add]`)

```
WARNING: No coverage data was generated during test run. Exiting now with no coverage report
```

Suggested steps:
1. Make sure that after the test runs, the daemon is exiting cleanly as the coverage data is dumped only during process exit

```
Console messages show coverage data was generated. However no report is present at coverage/html/index.html

For example: Lines similar to the following are seen but report is missing -
Summary coverage rate: lines......: 0.1% (50 of 35559 lines) functions..: 0.5% (14 of 2882 functions) branches...: no data found.
Creating HTML report
```

Suggested steps:
1. We have seen this occur sometimes when the report generating tool (genhtml) fails as it is unable to find the file "dirs.c.in" in the library path.
   A simple workaround is to copy this file to /lib.
   It is not needed to add ops-openvswitch repo to your sandbox. Simply copying this file to /lib is good enough.
```
   sudo cp src/ops-openvswitch/lib/dirs.c.in /lib/
```

```
Not receiving good coverage numbers though a test case with "systemctl stop <ops-myrepo>" is being executed as the last test-case
```

Suggested steps:
1. Make sure that after the test runs, the daemon is exiting cleanly as the coverage data is dumped only during process exit.
2. In many cases, the daemons have handlers for unixctl exit commands wherein it does a clean exit (with exit(0)).
   In such cases, use "ovs-appctl -t <daemon> exit" to hit this code-path.
   In short, make sure that the daemon does a clean exit in the code-path hit after triggering the daemon exit command.

```
Not getting coverage for directories contributing towards plugins
```
Few directories in a repo contribute towards a plugin that is loaded by a different daemon.
For example, code under src/cli contributes towards a .so plugin loaded by vtysh.
Or code under ops-classifierd/src/acl/switchd-plugin contributes towards a .so plugin loaded by the ops-switchd daemon.
Plugins run within the context of the daemon that loads them.
Hence one needs to make sure that this daemon exits cleanly to get coverage numbers for these directories.
In above cases, vtysh or ops-switchd need to exit to get coverage numbers under src/cli or under ops-classifierd/src/acl/switchd-plugin respectively.

Suggested steps:
1. The test-framework will force a vtysh exit at the end of the test script. This is in works.
2. Make sure the daemon loading the plugins exits cleanly.

#### Generate coverage report for Python-based code

##### Requirements

1. Code coverage for Python uses `testenv_run` target internally to run the FT/CTs that produce the coverage data. Therefore it is required to initialize the testenv prior to running the coverage target, i.e.:

    ```
    make testenv_init
    ```
2. The `coverage`  tool has to be integrated in OPS build system. The recipe file for coverage tool is located at [devtools](http://git.openswitch.net/cgit/openswitch/ops-build/tree/yocto/openswitch/meta-foss-openswitch/recipes-devtools/python).  The `coverage` tool must be packaged inside build. It can be done by adding `python-coverage` to *yocto/openswitch/meta-distro-openswitch/recipes-core/packagegroups/packagegroup-openswitch.bb*.

    ```
    RDEPENDS_packagegroup-ops-min = "\
    python \
    ...
    python-pyroute2 \
    python-coverage \
    ...
    "

    ```
3. Modify *tools/topology/tox.ini* to remove the `--random` flag. With random flag enabled, the testcases will be shuffled and hence, it will be hard to decide when to dump the coverage data file.
4. Create a directory in the *src/daemon_name/coverage_report* to dump the coverage data file after execution of the testcase.

##### Running the coverage report

The coverage report generation requires the testcases to be modified in following way:

 1. In the first `test_` function, kill the daemon and restart it with `coverage run` prefix to execute coverage tool on a given daemon. See the model example of *test_restd_ct_bgp_neighbors.py*. Provide the source path in the coverage command for which coverage report needs to be collected. The `--append` or `-a` flag is used to append the coverage data to `.coverage` data file during each testcase run.
 ```
def test_restd_bgp_neighbors_get_bgp_neighbors(setup, sanity_check,
                                               topology, step):
        sw1 = topology.get("sw1")
        assert sw1 is not None

        global dir
        dir = "/ws/manesay/rest_0701/genericx86-64/src"
        global daemon_name
        daemon_name = "restd"
        global dir_name
        dir_name = "ops-restd"
        global file_name
        file_name = dir_name + "/restd.py"

        sw1("systemctl stop " + daemon_name, shell="bash")
        sw1("cd " + dir, shell="bash")
        sw1("coverage run -a --source " + dir_name  + " " + file_name +  " &", shell="bash")
 ```

 2. Wait till the daemon comes back up with coverage enabled.
 ```
         sleep(10)
 ```
 3. In order to dump the coverage report kill the daemon when all testcases ran i.e. in the last `test_` function.
 ```
        sw1 = topology.get("sw1")
        assert sw1 is not None

        sw1("cd " + dir, shell="bash")
        sw1("ps -ef | grep " + daemon_name + " | grep -v grep | awk '{print $2}' | xargs kill -2", shell="bash")
        sw1("cp .coverage " + dir_name + "/coverage_report/.coverage", shell="bash")

 ```
 4. This step can be optional for most of the daemons. For specific daemons like `restd`, delete_teardown gets called once all testcases are ran. So, we need to restart the daemon in normal mode using `systemctl start <daemon_name>`. Wait until daemon is ready and continue running the teardown part.

 ```
        sw1("systemctl start " + daemon_name, shell="bash")
        sleep(10)
 ```
 5. Start running testcases, i.e.
 ```
 make testenv_run component ops-restd
 ```

 6. Look for file `.coverage`  in the same directory you created earlier.
 7. Then, run `coverage report -m` to generate the code coverage report.

##### Troubleshooting

If you run the `coverage report -m` from your VM. You might see following error:
```
Couldn't read data from '/ws/manesay/rest_0701/genericx86-64/src/ops-restd/coverage_report/.coverage': UnicodeDecodeError: 'utf-8' codec can't decode byte 0x80 in position 0: invalid start byte
```
To resolve the above error add following lines to the last testcase before restarting the daemon and it will generate report on docker where same decoding is used.

```
    sw1("cd coverage_report -m", shell="bash")
    sw1("coverage report > coverage_report.txt")
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
All registered users can create _feature branches_. The only requirement
is that these branches are named `feature/{name}`, where `{name}` is
chosen by you. If you try to create a branch and you get an error from
Gerrit stating that you don't have enough permissions, you can check the
[All Projects](https://review.openswitch.net/#/admin/projects/All-Projects,access)
access page and make sure that "Create Reference" is listed under
`Reference: refs/heads/feature/*`.

### Creating a branch in your repository
Assume you are creating a branch `foo` on the project `sysd`, you would use the following commands:

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
=======
Do not forget the branch name after `git review`. It is _not_ enough to
have a local branch named the same as the branch that you want to
publish the changes to, it is _necessary_ to indicate this when you
create the code review.

If you are a developer planning to merge your branch (source) to another branch (target):

```
make devenv_add sysd
cd src/sysd
git checkout <target>
git pull # update the local repo to find any new remote branches
git merge --no-ff <source> # merge the source branch changes to the target
git commit -s --amend # this will modify the commit message to include a ChangeId. Without the ChangeID, commits will not go through
git review <target> # send the review for target branch. The review for merge will not have any code as this is only a merge
```

### Updating ops-build to use your new branch

When you publish a new review, the CIT infrastructure will automatically
try to build the new code. If followed the above procedure to publish
your code to a branch, what will happen is that a fresh copy of
`ops-build` is cloned and it's used to build your new commit. If there's
a branch in `ops-build` with the exact same name as your target branch,
then it's used for building, otherwise `master` is used.

The above generally works if your branch coexists happily with the rest
of the code. If on the other hand you have closely related changes in
two or more different repositories (e.g. your repository and the schema;
your repository and CLI; etc) then you will need to create a branch in
`ops-build` in order to express your dependencies.

Open the recipe file (the `.bb` or `.bbappend` file) for your component.
There you should find a line like the following:

```
SRC_URI=git://git.openswitch.net/openswitch/ops-quagga;protocol=http
```

Append `;branch=feature/foo` to the SRC_URI, like this:

```
SRC_URI=git://git.openswitch.net/openswitch/ops-quagga;protocol=http;branch=feature/foo
```

You also want to change the `SRCREV` variable so that instead of commit
hash, it tracks `HEAD` in your branch:

```
SRCREV="${AUTOREV}"
```

Commit these changes and publish the code review:

```
git review feature/foo
```

When you are ready to merge your branch back to `master` please keep in
mind that if you don't have any other changes in your recipe, you don't
need to merge this branch back to `master`, it's enough to update the
`SRCREV` of the corresponding recipes. If you _do_ have other changes
that you need to preserve, please remember to undo these changes before
merging.

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
