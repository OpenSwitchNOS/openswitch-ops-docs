# How to contribute to the code

Follow the [Getting Started](./getting-started.html) guide to prepare your system to be able to contribute to the OpenSwitch project.

## Contents

- [New code](#new-code)
	- [Adding a New Repository](#adding-a-new-repository)
		- [Writing a new daemon with CMake](#writing-a-new-daemon-with-cmake)
		- [Adding a recipe for your daemon](#adding-a-recipe-for-your-daemon)
- [Contributing changes](#contributing-changes)
	- [Configuring the environment](#configuring-the-environment)
		- [Create SSH keys](#create-ssh-keys)
		- [Add the SSH Public Key to Gerrit](#add-the-ssh-public-key-to-gerrit)
		- [Verify that an username is assigned to Gerrit](#verify-that-an-username-is-assigned-to-gerrit)
		- [Client SSH configuration](#client-ssh-configuration)
		- [Working behind a firewall](#working-behind-a-firewall)
	- [Configuring Git and Gerrit](#configuring-git-and-gerrit)
	- [Installing git-review](#installing-git-review)
	- [Preparing changes to be reviewed](#preparing-changes-to-be-reviewed)
	- [Sending changes for review](#sending-changes-for-review)
	- [Resubmitting a set of changes](#resubmitting-a-set-of-changes)
	- [After changes have been approved by Reviewers](#after-changes-have-been-approved-by-reviewers)
- [Documenting the code](#documenting-the-code)
  - [Documents distribution](#documents-distribution)
- [Commit messages](#commit-messages)
	- [Git commit message guidelines](#git-commit-message-guidelines)
		- [Subject](#subject)
		- [Body](#body)
		- [Use cases](#use-cases)
		- [Bug fixes](#bug-fixes)
		- [Changes](#changes)
	- [Using Gitchangelog](#using-gitchangelog)
- [Coding Style](#coding-style)
	- [Comparisons](#comparisons)
	- [Variables](#variables)


## New code
OpenSwitch preferred build system for C/C++ applications is `CMake`.

### Adding a New Repository
The following are the steps to follow in order to add a new repo to the OpenSwitch project, referred as `<repo-name>`.

1) Git clone project:
```bash
git clone https://review.openswitch.net/infra/project-config
```

2) Create a new file `gerrit/acls/openswitch/ops-<repo-name>.config` with following contents.

**Note:** The code review and abandon group name should be `<repo-name>`-maintainers, as shown in this example:

```bash
[access "refs/heads/*"]
abandon = group Change Owner
abandon = group ops-<repo-name>-maintainers
label-Code-Review = -2..+2 group ops-<repo-name>-maintainers
label-Workflow = -1..+1 group Change Owner
```

3) The `ops-<repo-name>-maintainers` group for your repo will get created by the `project-config-maintainers` when they approve the code review.

4) Modify `gerrit/projects.yaml` to add the repository.
```html4strict
- project: openswitch/<repo-name>
  description: <Repo Description>
```

5) Git commit the changes.

**Note:** In the commit message please specify the full name for two users in `Git Hub` that you wish to add as maintainers of this repo.
```bash
git commit --signoff
```

6) Git review the changes
```bash
git review
 ```

#### Writing a new daemon with CMake

The new repo needs a CMake file to be able to index with the rest of the OpenSwitch project.
The easiest way to create a CMake file is to take a similar repo, copy the CMake and adapt it for the new repo.
The following is an example code taken from vland.

```bash
# Copyright (C) 2015 Hewlett-Packard Development Company, L.P.
# All Rights Reserved.
#
#    Licensed under the Apache License, Version 2.0 (the "License"); you may
#    not use this file except in compliance with the License. You may obtain
#    a copy of the License at
#
#         http://www.apache.org/licenses/LICENSE-2.0
#
#    Unless required by applicable law or agreed to in writing, software
#    distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
#    WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
#    License for the specific language governing permissions and limitations
#    under the License.

cmake_minimum_required (VERSION 2.8)

set (VLAND vland)
project (${VLAND})
set (SRC_DIR src)
set (INCL_DIR include)

# Rules to locate needed libraries
include(FindPkgConfig)
pkg_check_modules(OVSCOMMON REQUIRED libovscommon)
pkg_check_modules(OVSDB REQUIRED libovsdb)

include_directories (${PROJECT_BINARY_DIR} ${PROJECT_SOURCE_DIR}/${INCL_DIR}
                     ${OVSCOMMON_INCLUDE_DIRS}
)

# Define compile flags
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -Wall -Werror")

# Source files to build vland
set (SOURCES ${SRC_DIR}/vland.c ${SRC_DIR}/vland_ovsdb_if.c)

# Rules to build vland
add_executable (${VLAND} ${SOURCES})

target_link_libraries (${VLAND} ${OVSCOMMON_LIBRARIES} ${OVSDB_LIBRARIES}
                       -lpthread -lrt)

# Rules to install vland binary in rootfs
install(TARGETS ${VLAND}
        RUNTIME DESTINATION bin)

```

For more details of how CMake works, please go to [CMake documentation](http://www.cmake.org/documentation/).

####  Adding a recipe for your daemon
It has to be a recipe for your deamon to work properly. This recipe should be placed in the `ops-build` repo, inside `yocto/openswitch/meta-distro-openswitch`.

There are different directories for the recipes:

- recipes-asic
- recipes-core
- recipes-kernel
- recipes-networking
- recipes-onie
- recipes-ops

Choose the directory that applies and add the `.bb` file in there.

This is an example of a recipe

``` bash
SUMMARY = "Management Interface Configuration Daemon"
LICENSE = "Apache-2.0"

LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/Apache-2.0;md5=89aea4e17d99a7cacdbeed46a0096b10"

DEPENDS = "ops-utils ops-ovsdb"

RDEPENDS_${PN} = "python-argparse python-json python-ops-ovsdb python-distribute"

SRC_URI = "git://git.openswitch.net/openswitch/ops-mgmt-intf;protocol=http \
           file://mgmt-intf.service \
         "

SRCREV="${AUTOREV}"

# When using AUTOREV, we need to force the package version to the revision of git
# in order to avoid stale shared states.
PV = "git${SRCPV}"

S = "${WORKDIR}/git"

do_install_prepend() {
     install -d ${D}${systemd_unitdir}/system
     install -m 0644 ${WORKDIR}/mgmt-intf.service ${D}${systemd_unitdir}/system/
}

SYSTEMD_PACKAGES = "${PN}"
SYSTEMD_SERVICE_${PN} = "mgmt-intf.service"

inherit openswitch setuptools systemd
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

If you need more details of how the BitBake works, please go to [BitBake documentation](https://www.yoctoproject.org/tools-resources/projects/bitbake).


## Contributing changes
Changes to the OpenSwitch code base go through a review process before being merged. This page describes how local modifications can be submitted for review and, if approved, made available upstream in the OpenSwitch project.

### Configuring the environment

OpenSwitch runs [Gerrit](https://code.google.com/p/gerrit/) for online code reviews and [Git](http://git-scm.com/) as the distributed version control system.
The [OpenSwitch Gerrit](https://review.openswitch.net/) site is the entry point for change, review, test and inclusion in one of the OpenSwitch projects.

#### Create SSH keys
Do this only if no SSH keys have been created earlier for this development machine. To find out if keys exist for this development machine check the contents of the `~/.ssh/` directory. The following two files will exist if keys were already created:
* `id_rsa`
* `id_rsa.pub`

If the `~/.ssh` directory or the files do not exist, then proceed to generate your SSH keys with the commands below:
```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa
```

You will be prompted for a location to save the keys, and a pass-phrase for the keys. Accept the default location to save the keys and provide a pass-phrase. The pass-phrase is important as it keeps your keys secure while stored in your hard-drive. Do remember the pass-phrase as it will be needed to work with Gerrit. An example output is the following:
```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/b/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/b/.ssh/id_rsa.
Your public key has been saved in /home/b/.ssh/id_rsa.pub.
```

The public key needed to work with Gerrit is saved at `~/.ssh/id_rsa.pub`. The contents of this file (this is a text file) will be needed in the next step.

If you get the `Agent admitted failure to sign using the key.` error message as seen below, simply run the command `ssh-add` after the error message.

```bash
"Agent admitted failure to sign using the key."
# debug1: No more authentication methods to try.
# Permission denied (publickey).
```

#### Add the SSH Public Key to Gerrit
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
 * OpenSwitch uses a `GitHub` account to login to the Review Site (Gerrit). If you do not have an account create one by cliking on `Sign up` in the GitHub page after you try to login to the [OpenSwitch Review](https://review.openswitch.net/).
 * Keep record of the password and username as this is your Gerrit user and is needed to work with OpenSwitch.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Select `SSH Public Keys` in the panel on the left.
* Paste the SSH public key (`id_rsa.pub` contents) in the input area for use with this site. The instructions for generating an SSH key are accessible just above the input area.
* Click `Add`

#### Verify that an username is assigned to Gerrit
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Click on the `Profile` link (left side)
* Verify that an username exists, or define one if it does not exist.
* Keep track of the username as this is the Gerrit User and will be used to work with OpenSwitch

#### Client SSH configuration
* Add an entry similar to this to your `~/.ssh/config` file (preserve the indentation), which directs SSH to the proper SSH private key file to use for the OpenSwitch review website:
```bash
Host review.openswitch.net
  User <gerrit-user>
  IdentityFile ~/.ssh/id_rsa
```

#### Working behind a firewall
The network port used by Gerrit (29418) on the review website may be blocked in some networks, for example on a corporate network. One workaround (besides asking your ISP or corporate IT team to open the port) is to tunnel using your HTTP proxy. You can achieve that with the `nc` or `socat` command line utilities, or any other of your preference. Follow the instructions below to use `socat` or `nc` to configure a proxy for the SSH traffic.

Take note of the proxy information.
* Proxy URL (e.g web-proxy.location.domain.com). Referred as `<proxy-url>` below.
* Proxy Port (e.g 8080). Referred as `<proxy-port>` below.

Using `nc`
* Install `nc` if not yet installed
* Open (or create) the `~/.ssh/config` file and add the following lines (preserve the indentation):
```
Host review.openswitch.net
   ProxyCommand nc -x <proxy-url>:<proxy-port> -X connect %h %p
```

Using `socat`
* Install `socat` if not yet installed
* Open (or create) the `~/.ssh/config` file and add the following lines (preserve the indentation):
```
Host review.openswitch.net
   ProxyCommand socat - PROXY:<proxy>:%h:%p,proxyport=<proxy-port>
```

For more details and examples for adding user/passwords, see [this page](http://gitolite.com/git-over-proxy.html).

### Configuring Git and Gerrit
You need to configure your local repository to use your Gerrit account.
* Set `gitreview.username` with your Gerrit login.
```bash
$ git config --global gitreview.username <gerrit-user>
```
* Configure email and name in Git:
```bash
$ git config --global user.email <email>
$ git config --global user.name <name>
```

If your username for Gerrit is different from the local machine login, export the `REVIEWUSER` variable. Preferable in some place where it gets always setup, like `~/.bashrc`. (*gitreview.username* will be used in preference to the *REVIEWUSER* environment variable if both are set).
```bash
$ export REVIEWUSER=<gerrit-user>
```

### Installing git-review
`git-review` is used to contribute changes back to the OpenSwitch project.

Install `git-review` as described [here](http://www.mediawiki.org/wiki/Gerrit/git-review), if not already installed on the development machine.

* Setup git-review
```bash
$ git-review -s
```

### Preparing changes to be reviewed
Before attempting to commit changes, make sure that they are compliant with the [OpenSwitch Coding Style](#openswitch-coding-style). For non-OpenSwitch modules, follow the coding style that the module already uses.

In particular,  the following files be rejected:
* Files with trailing spaces
* Files with with non-printable ASCII characters in their names.

Changes for review should be **committed to a local branch** with a commit message that:
* Begin with a single line of text which summarizes the contents of the change.
* Additionally provide a description following the summarization line.

If the change set has been composed by multiple commits to the local branch, **consider squashing them into a single commit** using `git rebase`.

### Sending changes for review
`git-review` is used as an aid when submitting a change set for review.
The commit message for the set of changes must include a `Change-Id`, as generated by the `-i` option to `git-review`. To send changes for review, commit the changes and then run git-review as follows:
```bash
$ git commit -s -m "Meaningful summary of this change set."
$ git-review -i
```
The `git-review` command output includes an URL for the change review. In example below the URL generated was: https://review.openswitch.net/1148.
```bash
$ git review
remote: Resolving deltas: 100% (1/1)
remote: Processing changes: new: 1, refs: 1, done
remote: (W) 5421c73: commit subject >65 characters; use shorter first paragraph
remote:
remote: New Changes:
remote:   https://review.openswitch.net/1148
```
Use the URL in the `git-review`output to login to the Review Site and add the list of reviewers.

### Resubmitting a set of changes
Reviewers may request modifications to the set of changes previously submitted. Make the changes as requested by the reviewers and then submit a patch with the following commands. The `-i` switch for `git-review` is not needed as a `Change-Id` was previously generated by the initial commit.
```bash
$ git commit --amend
$ git-review
```

It is also possible to abandon a set of changes using the web interface, if that is desired.

### After changes have been approved by Reviewers
* Log in to the change review URL using the link provided by the `git-review` command output.
* Click on the `Review` button and give the change a `+1 Approved` rate in the `Workflow` section.
* This initiates the process of merging your change with the main product documentation.

## Documenting the code

The OpenSwitch project uses the Markdown markup language to generate its documentation.
All documentation that is contributed to the project needs to be written in this format so that it can get built into the project.

### Documents distribution

The following table lists the type of documents, the  target locations and the expected file names. Find below the table a small description of each type of document.

| Doc Type                	| Repo                     	 | Directory 	| File Name               	  |
|-------------------------	|--------------------------	 |-----------	|-------------------------	  |
| User Guides            	  | openswitch/ops           	 | /docs     	| [feature]_user_guide.md 	  |
| Command References      	| openswitch/ops           	 | /docs     	| [feature]_cli.md        	  |
| Feature Designs         	| openswitch/ops           	 | /docs     	| [feature]_design.md     	  |
| Feature Test Plans      	| openswitch/ops           	 | /tests    	| [feature]_test.md       	  |
| Component Functionality 	| openswitch/ops-[component] | /         	| README.md               	  |
| Component Design        	| openswitch/ops-[component] | /         	| DESIGN.md               	  |
| Component Test Plan     	| openswitch/ops-[component] | /tests    	| [component]_test.md         |
| Infrastructure            | opeswitch/ops-build        | /docs      | [Infrastructure section].md |

- **User Guides:** It documents how to use the different OpenSwitch features.
- **Command References:** It documents the command usage for the different features found in the OpenSwitch project.
- **Feature Designs:** It provides a high level design description for the feature.
- **Feature Test Plans:** It contains a high level overview of the test plans for each feature.
- **Component Functionality:** It describes the content of the component on this repo and provides pointers to other documents.
- **Component Design:** It describes a high level design of the component.
- **Component Test Plan:** It contains all the component tests documentation relevant to the specific repository.
- **Infrastructure:** It describes a process in the core from OpenSwitch. These documents are addressed for developers that want to contribute with the code and only use OpenSwitch.

## Commit messages

Commit messages for change sets are recorded in the git repository when the changes are merged. Keeping with a simple, consistent format for these messages makes it easier for other users (and tools) to understand the changes.

The following excerpt is from the [Git Pro, 2nd Edition](http://git-scm.com/book/ch5-2.html#commit_guidelines) book, chapter 5. We gratefully acknowledge the book's [CC BY-NC-SA 3.0](http://creativecommons.org/licenses/by-nc-sa/3.0/legalcode) license which permits replication without providing endorsement.

> Getting in the habit of creating quality commit messages makes using and collaborating with Git a lot easier. As a general rule, your messages should start with a single line that’s no more than about 50 characters and that describes the changeset concisely, followed by a blank line, followed by a more detailed explanation. The Git project requires that the more detailed explanation include your motivation for the change and contrast its implementation with previous behavior – this is a good guideline to follow. It’s also a good idea to use the imperative present tense in these messages. In other words, use commands. Instead of “I added tests for” or “Adding tests for,” use “Add tests for.” Here is a template originally written by Tim Pope:

```
Short (50 chars or less) summary of changes

More detailed explanatory text, if necessary.  Wrap it to
about 72 characters or so.  In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.

Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Typically a hyphen or asterisk is used for the bullet,
    preceded by a single space, with blank lines in
    between, but conventions vary here
```
>If all your commit messages look like this, things will be a lot easier for you and the developers you work with.

**Note**: there is no period ('.') at the end of the summary (first) line.

### Git commit message guidelines

The commit messages are of great importance on a development process. They help the developers and final users to understand why changes were made. Therefore it is important to follow these guidelines. The first thing to take into account is that ALL our commit messages must have a subject and a body, explained in detail in the next sections.
The commit messages should been write impersonal. Use verbs like _Add_, _Fix_, _Change_, _Update_ or _Implement_. **Don't** use _Added_ or _Adding_, for example. Another important point is that each commit must change/add/fix only one thing. Keep in mind that each published commit, will live forever and sometime when you go back to the project history for some issue, it could save you the day due to a good hint in an informative commit message.

#### Subject

The first line of the commit message is known as the subject. It describes the change briefly and helps reviewers to see at a glance what the commit is about. It should be no more than 50 characters (must be less than 80). Since we are using gitchangelog (a open source tool that generates changelogs) the subject must have the following format:

```
act: aud: Here comes the subject of the commit @tag
```

Where _act_ (action) classifies the type of changes made in the current commit and could be:
- 'new' is used to say that new features were added to the code
- 'fix' is used to say that there was a bug fixed
- 'chg' is used to say that changes (refactor, small improvement, cosmetic changes) have been made to the code.

The keyword _aud_ determines the intended audience of the commit message could be:

- 'dev' the commit message is intended to developers
- 'usr' the commit message is intended to the final user

If a subject has a tag the commit will not appear in the changelog. _tag_ could be:

- 'minor' is a minor change that you don't want to show in any changelog
- 'cosmetic' is cosmetic change like delete white spaces, indent the code or edit the comments; and you don't want this commit to appear in the any changelog
- 'refactor' is refactor you don't want to appear in the any changelog
- 'wip' is work in progress you don't want to appear in any changelog

**All the commit messages with the audience usr are parsed to generate the release notes. Be extra careful with those messages!.**

#### Body

Use the body of the commit message to describe your commit in detail. And follow these guidelines:

- Separate the body from the subject with an empty line.
- Give an overview of why you're committing this change.
- Don't use bullet points.
- What the commit changes.
- Any new design choices made.
- Areas to focus on for recommendations or to verify correct implementation.
- Any research you might have done.
- Wrap the body of the message between 70 and 100 characters.

#### Use cases

The general format of a commit message should look like:

```
act: aud: Short description of the work done in this commit

Explanation of the work done in this commit. This explanation
must explain in detail the change. Don't use bullet points.
```

#### Bug fixes

If the bug #1043 has been fixed and you want it to be in the release notes then its commit message should look like:

```
fix: usr: Fix Bug #1043. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

If the same bug has been fixed but you don't want it in the release notes then you'll have to change _usr_ to _dev_

```
fix: dev: Fix Bug #1043. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

If more than one bug has been fixed with the same change then its commit message should look like:

```
fix: usr: Fix Bugs #1043, #1045 and #1056. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

**If each bug requires a separate change then _don't_ put all them together in the same commit, you should do a commit for each bug.**

#### Changes

You have made a small improvement on the pv_pm_neo_indirectLookUpEntry and you want to share it with you coworkers but not with the final user (release notes). Your commit message should look like:

```
hg: dev: Change pv_pm_neo_indirectLookUpEntry SPs handling (no ucx support)

Move ind_lut entries for extension mode to entries 125 & 126
(now INDLUT_MODE_ENTRY on pm.h can be used only to change it)
```

After you did this commit you realize that the name of a non-important variable should be change. The message of the commit changing the name of the variable should look like:

```
chg: dev: Change pv_pm_neo_indirectLookUpEntry variable name @minor

Change the name of the variable the variable old_name that stored the
a previous state to new_name.
```

Maybe you need to edit some comments in the code. Then your commit message should look like:

```
chg: dev: Edit comments of pv_pm_neo_indirectLookUpEntry @cosmetic

Edit the comments of the pv_pm_neo_indirectLookUpEntry. Explain the usage
of some variables inside the function function_name.
```

Or you need to refactor the code and you don't want that commit to appear in any changelog. Then your commit message should look like:

```
chg: dev: Refactor pv_pm_neo_indirectLookUpEntry @refactor

Change the way the loop works to save clock cycles.
```

All the commits with a commit message subject with a tag like **@minor**, **@cosmetic** and **@refactor** won't appear in any changelog.

**Don't include many different changes into one commit. Each change must have it's separate commit.**

### Using Gitchangelog

Gitchangelog is an open source project that parse the git log to generate formated changelogs. We use this tool to autogenerate the release notes. The API repository has a ChangeLog file updated when each release is done. If you want to see the current ChangeLog (changes since last release plus the unreleased changes) you can run the gitchangelog script from the API repository. Before running the script you'll have to install mako:

```bash
sudo apt-get install python-mako
```

To run the script from the repository:

```bash
python <pvapi path>/tools/scripts/gitchangelog/gitchangelog.py
```

If you want to install gitchangelog on your computer run:

```
sudo apt-get install python-setuptools
cd <pvapi path>/tools/scripts/gitchangelog
sudo ./autogen.sh && sudo python setup.py install
```

Now you can simply run

```
gitchangelog
```

Inside the API repository and you'll see the current changelog.


## Coding Style
The coding style used for OpenSwitch is an extension of the [Open vSwitch Coding Style](https://github.com/openvswitch/ovs/blob/master/CodingStyle.md), which is itself an extension of the [One True Brace Style](https://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS) for indenting. The additions below are clarifications and do not conflict with the Open vSwitch style.

### Comparisons
Write comparisons in a form that reads naturally. Examples:
```c
 bool foo_b = some_bool_function(x);
int foo_i = some_int_function(x);

if (true == foo_b)   /* No, unnecessary comparison and doesn't read well */
if (foo_b)           /* Yes */

if (11 == foo_i)     /* No, doesn't read well */
if (foo_i == 11)     /* Yes */

if (foo_i == 0)      /* Yes */
if (!foo_i)          /* Yes */
```

### Variables
Do not do gratuitous initialization of variables.  Doing this prevents the compiler from detecting accidental use before initialization.

```c
struct Goofus *goofus = NULL;   /* Wrong */
struct Gallant *gallant;        /* Right */
```
