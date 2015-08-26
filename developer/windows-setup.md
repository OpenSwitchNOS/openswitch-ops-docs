#  How to contribute to the OpenSwitch documentation in Windows

## Contents
- [Clarification](#clarification)
- [Documentation changes work-flow summary](#documentation-changes-work-flow-summary)
- [System Requirements](#system-requirements)
- [Choose an appropiate Text Editor](#choose-an-appropiate-text-editor)
- [About Git and Gerrit](#about-git-and-gerrit)
- [Install Git for Windows](#install-git-for-windows)
- [Installing SSH Keys](#installing-ssh-keys)
	- [Generate the SSH keys](#generate-the-ssh-keys)
	- [Create an account in the Review Site](#create-an-account-in-the-review-site)
	- [Upload the SSH key](#upload-the-ssh-key)
- [Check the connection](#check-the-connection)
- [Configure a proxy (if required)](#configure-a-proxy-if-required)
	- [Install socat](#install-socat)
	- [Configuring the proxy](#configuring-the-proxy)
- [Install git-review](#install-git-review)
	- [Install Python](#install-python)
	- [Install git-review](#install-git-review)
	- [Configure the proxy for git-review installation](#configure-the-proxy-for-git-review-installation)
- [Clone the documentation repository](#clone-the-documentation-repository)
- [Set up git-review](#set-up-git-review)
- [Contribute changes to the documentation](#contribute-changes-to-the-documentation)
	- [Contribution work-flow](#contribution-work-flow)
	- [Making changes to the documentation](#making-changes-to-the-documentation)
	- [Adding the changes to the local copy of the repository and sending the documents for review](#adding-the-changes-to-the-local-copy-of-the-repository-and-sending-the-documents-for-review)
	- [Adding the document maintainers to the list of reviewers for the change](#adding-the-document-maintainers-to-the-list-of-reviewers-for-the-change)
	- [Responding to feedback and re-sending the document for review](#responding-to-feedback-and-re-sending-the-document-for-review)
	- [After the document maintainers  have approved the changes](#after-the-document-maintainers-have-approved-the-changes)

## Clarification
Windows can be used to contribute to the OpenSwitch documentation only. To contribute to or build the project, a GNU/Linux based operating system is required (a VM running Linux in your Windows workstation works too). The information at [Getting Started with OpenSwitch](./getting-started.html) provides the instructions to contribute to and build OpenSwitch using a GNU/Linux operating system.

## Documentation changes work-flow summary
In a nutshell,  this is the work-flow for documentation contribution:

* The contributor needs to clone the Git repository where the documentation lives.
* The contributor makes changes to the project documentation.
* The contributor sends the changes for review to the [Gerrit Review](https://review.openswitch.net/) site.
* The community reviews the changes in the Gerrit Review site
 * Provide feedback if further changes are required
 * Approve the changes and merge them to the project
* If feedback is received from the community and changes are required, the contributor works on the changes and send the document again for review.
 * Do  this until the changes are approved by the community.

## System Requirements
To start working in Windows you need a few tools and to follow some procedures.

* Choose an appropriate Text Editor.
* Install Git for Windows.
* Install the SSH keys.
* Check the connection.
* Configure a proxy (if required).
* Install git-review.
* Clone the documentation repository.
* Set up git-review.

The instructions are provided below.

## Choose an appropiate Text Editor
The OpenSwitch documentation is  written using the  MarkDown text file format. To contribute to the OpenSwitch documentation you will need a text editor that supports MarkDown files.

To process MarkDown files the following tools are recommended (only one is required).

* **Atom**: A Text Editor with MarkDown support that requires installation [https://atom.io/](https://atom.io/)
* **StackEdit**: A Web-based Text Editor that does not require installation [https://stackedit.io/](https://stackedit.io/)

For further information about those, or other tools, please refer to the tool's web site.

## About Git and Gerrit
**Git** is the version control system where OpenSwitch maintains its sources.
**Gerrit** Is the review tool that the OpenSwitch project uses for its  documentation and source code.
**Git Bash Prompt** is a Command Prompt (also known as Command Line) tool that after having installed Git (see instructions below) will be available by right clicking on a directory (such as your Desktop) and choosing on the contextual menu the option `Git Bash Here` . This Command Prompt will be used to execute Git related commands.

## Install Git for Windows
The Git for Windows package is needed to work with GIT repositories. The OpenSwitch documentation lives on a Git repository and it is necessary to clone the GIT repository in order to contribute to the project.

* Download the Git For Windows latest package. The following link is suggested: [Git for Windows](https://git-for-windows.github.io/).
* Start the package installation
* Accept the `Select Destination` default setting.
* Accept the `Select Components` default setting.
* In the `Adjusting your PATH environment` setting, select `Use Git from the Windows Command Prompt` option. Choosing this option will make it easy to run Git commands from the Command Prompt.
* For the rest of the options, accept the default settings.
* Accept the installation.

## Installing SSH Keys
The SSH keys are needed to establish a secure connection between the client workstation and the remote repository where the documentation lives.
It is necessary to generate an SSH key, create a web account in the Review Site and then upload the SSH keys to the Review site following the procedure below.

### Generate the SSH keys
* Right click on the Desktop.
* On the contextual menu, select `Git Bash Here` This action opens a new command line window.
* Run the command
```bash
$ ssh-keygen
```
* It will prompt you with a few questions, press Enter to accept all the default values.
* This actions saves the SSH public key in the following location `C:\Users\<username>\.ssh\id_rsa.pub` where `<username>` is your Windows login.

### Create an account in the Review Site
OpenSwitch uses a `GitHub` account to login to the Review Site (Gerrit). If a `GitHub` account already exists for other projects it is not necessary to create a new one specifically for OpenSwitch. In that case, skip this step.

* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
* If you do not have an account create one by cliking on `Sign up` in the GitHub page after you try to login to the [OpenSwitch Review](https://review.openswitch.net/).
* Keep record of the password and username as this is your Gerrit user and is needed to work with OpenSwitch.
 * The user will be referred as the **`<gerrit-user>`** throughout this document.
* Validate that the account has been created successfully by visiting the [Review Site](https://review.openswitch.net/#/) and signing-in with the `<gerrit-user>` account.
 * To sign-in in click on the `Sign In` link at the top-right corner

### Upload the SSH key
The SSH key created needs to be uploaded to the review site at [Gerrit](https://review.openswitch.net/#/)

* Locate your key that was created under the location `C:\Users\<username>\.ssh\id_rsa.pub` where `<username>`is your Windows login.
* Open the `id_rsa.pub` file with any Text Editor. Be careful not to open the file id_rsa (with no extension) as that file is meant to be kept for private use only.
* Copy all the contents of the file, the following is an example

```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDu8SQST6iukQhsOp2u/KREsnFDEldgNdFmOm3vnGKmjv7InEP+DDFWdMBxV6ZHpeJZq1kCHuSSMPAzovnkqjozmDwsVucAm30vMkREDMAq9SoA+q9/N2dAfugbyN5qo5sjfeLApLbpsyU3SVihhE2TulwxGMzPSVp/Ht/YvJj4YMvPRuS1JuK3ZNfaT2FAp8tRu0+cStyASyEJ8afEbk1vg7TNkNZT16Tg5bD9LdBQmVMaz/QW+vh6H2aEsz5w3kuWn3/s9JpRrQ2fIGO8J9SY1MTvBLRqMDFj7w1tKe1ae7nmxZT5pfS23w847AWfynZlwkNCwqKYaawsyZCi8BEb username@hostname
```

* Go to the SSH Keys page in the review site at [SSH Keys](https://review.openswitch.net/#/settings/ssh-keys)
* Click on  `Add Key ...` and paste the contents of the file in the `Add SSH Public Key` text-box
* Click on `Add` to complete the process

## Check the connection
* Take note of the Gerrit username. Referred as `<gerrit-user>` below.
 * The Gerrit username is **case sensitive**, make sure to verify your username in the Gerrit Review [Settings](https://review.openswitch.net/#/settings/) page.
* Right click on the Desktop.
* On the contextual menu, select `Git Bash Here` This action opens a new Git Bash Command Prompt.
* Run the command
```bash
$ ssh <gerrit-user>@review.openswitch.net -p 29418
```
 where `<gerrit-user>` needs to be changed for your user.
* In the prompt `Are you sure you want to continue connecting(yes/no)`, type `yes`
* If the connection is successful the following message will appear `**** Welcome to Gerrit Code Review ****`
* If the connection fails verify that all previous steps have been completed correctly. Make sure that the gerrit-user is been provided and not your computer user.
* It is possible that the connection is failing because the network port used by Gerrit (29418) is blocked in the network. Follow the steps below to configure a proxy to overcome this problem. After configuring the proxy check the connection again.

## Configure a proxy (if required)
The network port used by Gerrit (29418) on the review website may be blocked in some networks, for example on a corporate network. One workaround (besides asking your ISP or corporate IT team to open the port) is to tunnel using your HTTP proxy. `socat` is a tool that can accomplish this requirement. Follow the instructions below to install `socat` and configure a proxy.

### Install socat
* Download socat for Windows from [Socat](http://blog.gentilkiwi.com/downloads/socat-1.7.2.1.zip)
* Create the `C:\socat\` directory and extract the zip files (socat-1.7.2.1.zip) in it.
* Make sure that after extracting the file, the following file exists in the specified location `C:\socat\socat.exe`

### Configuring the proxy
* Take note of your computer user name, referred as `<username>` below.
* Take note of the corporate proxy information.
 * Proxy URL (e.g *web-proxy.location.domain.com*). Referred as `<proxy-url>` below. Do no include the protocol in front of the URL (i.e http://, https://).
 * Proxy Port (e.g *8080*). Referred as `<proxy-port>` below.
* Take note of the Gerrit username. Referred as `<gerrit-user>` below.
 * The Gerrit username is **case sensitive**, be sure to verify your username in the Gerrit Review [Settings](https://review.openswitch.net/#/settings/) page.
* Create (or modify) the `C:\Users\<username>\.ssh\config` file.
* Add the following information to the config file (preserve the indentation).
```
Host review.openswitch.net
    User <gerrit-user>
    IdentityFile C:\Users\<username>\.ssh\id_rsa
    ProxyCommand C:\\socat\\socat.exe - PROXY:<proxy-url>:%h:%p,proxyport=<proxy-port>
```
* Check the connection (see previous step). If the connection fails verify that socat has been properly installed and that the proxy has been properly configured.
* Common mistakes are:
 * To mix up the gerrit and computer user names.
 * To have a typo in the proxy URL or enter the incorrect port number.
 * To provide the proxy URL with the protocol and not just the name, e.g `http://proxy.location.domain.com` instead of just `proxy.location.domain.com`.
 * The Gerrit username is **case sensitive**, be sure to type it as it appears in Gerrit.

## Install git-review
git-review is the tool that sends the documentation changes to the OpenSwitch documentation repository for review. Python is required for git-review.

### Install Python
* Install Python or upgrade to the latest version (Python 3.x)
 * **Important:** Do not install Python in any directory with a space in its path as there is a bug, use the default, e.g. `C:\Python34\`
* Add the Python and the Python Scripts directory to the system PATH
 * Right Click on Computer and navigate to `Advanced system settings > Environment Variables > User variables`
  * Edit the `PATH` variable to add the Python and the Python scripts directory
  * Add the following to the beginning of the variable `C:\Python34\;C:\Python34\Scripts\;`
 * Different directories in the PATH are delimited by a semicolon ";". Do not add any whitespace to the PATH list.
 * If the installed version is not Python 3.4 update the PATH with the correct directory.

### Install git-review
* Run the Git Bash Promot as an Administrator
 * Locate the git-bash program `C:\Program Files\Git\git-bash.exe`, right click on it and choose `Run as administrator`
* Run the following command to install git-review
```bash
$ pip install git-review
```
* If the installation fails with an error similar to the one below it is probably caused by a missing proxy configuration. See the next step for directions.
```
Retrying ... after connection broken by ConnectTimeoutError pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection ... Connection to pypi.python.org timed out.
```

### Configure the proxy for git-review installation
If the workstation is connected to a network that requires a proxy to access the Internet;  configure it as follows and then re-attempt the installation of git-review with the command `pip install git-review`.

* Take note of the proxy information.
 * Proxy URL (e.g web-proxy.location.domain.com). Referred as `<proxy-url>` below
 * Proxy Port (e.g 8080). Referred as `<proxy-port>` below.
* To configure the proxy run the following commands in the Git Bash Prompt
```bash
$ export https_proxy=https://<proxy-url>:<proxy-port>
$ export http_proxy=http://<proxy-url>:<proxy-port>
```

## Clone the documentation repository
To download the documentation and be able to contribute, it is necessary to clone the repository where the documentation exists.

* Right click on the Desktop.
* On the contextual menu, select `Git Bash Here`
* Run the following command where `<gerrit-user>` needs to be changed for your user
```bash
$ git clone ssh://<gerrit-user>@review.openswitch.net:29418/openswitch/ops-docs openswitchdocs
```
* This command clones the documentation repository in your Desktop under a new directory called `openswitchdocs`

## Set up git-review
* Locate the `openswitchdocs` directory created for the OpenSwitch documentation repository (typically `Desktop\openswitchdocs`).
* Right click on the directory and then choose the option `Git Bash Here`.
* Run  the following commands to configure the account.
 * `<gerrit-email>` is the email used during the Gerrit user account creation.
 * `<gerrit-user>` is the Gerrit username and it is case-sensitive
```bash
$ git config --global --add gitreview.username <gerrit-user>
$ git config --global user.email <gerrit-email>
$ git config --global user.name <gerrit-user>
```
* Run the following command to set up git-review
```bash
$ git review -s
```
* Your local Git cloned repository is now ready to submit changes for review to the OpenSwitch project.

## Contribute changes to the documentation

### Contribution work-flow
The following is the work-flow of the documentation contribution for the OpenSwitch project (assuming the repositry has been cloned and git-review has been properly configured).

* The contributor makes changes to the project documentation.
* The contributor uses the git commands (git add, commit) to include those changes in the local  the repository.
* The contributor uses the review command (git review) to forward the change to the remote Gerrit Review system .
* The contributor uses the OpenSwitch remote Gerrit Review system web page to add the document maintainers to the list of reviewers for the change.
* The document maintainers review the documentation changes in the Gerrit Review system.
* The document maintainers provide feedback about the changes.
 * The document maintainers can approve (giving +1 or +2 rate to the change) or reject (giving -1 or -2 rate to the change) by adding comments via the Gerrit Review system.
* If the changes have been approved by the document maintainers.
 * The contributor uses the Gerrit Review system to finally approve the change
  * To finally approve the change, log in to the Gerrit Review system, clik on the `Review` button and give the change a `+1 Approved` in the `Workflow` section.
 * The Gerrit Review system sends the change to the project queue to be merged with the main product documentation.
* If the changes are rejected and feedback is provided by the document maintainers.
 * The contributor makes the changes to the documents according to the feedback.
 * The contributor uses the git commit command with the `--amend` switch to include those changes in the local copy of the repository.
 * The contributor uses the review command to include the new changes in the remote Gerrit Review system.
 * The document maintainers review the changes, approve them or reject them following the same process as above.

### Making changes to the documentation
* Locate the `openswitchdocs` directory created for the OpenSwitch documentation repository (typically `~\Desktop\openswitchdocs\`).
* There are two directories. Inside each directory there will be the documents for End Users and Developers.
 * developer
 * user
* Make changes to the existing documents or add new documents as needed.

### Adding the changes to the local copy of the repository and sending the documents for review
* Locate the `openswitchdocs` directory, right click on it and choose `Git Bash Here` to open a Git Bash Prompt.
* Run the following command to list all the documents that have been changed.
```bash
$ git status
```
* Example output where the file `doc-contributor-setup-windows.md` has been edited.
```bash
On branch master
...some omitted output here ...
        modified:   developer/doc-contributor-setup-windows.md
```
* If the file listed in the output is ready to be submitted for review, run the following commands:
```bash
$ git add <filename>
$ git commit -s -m "<a mandatory message with a description of the changes>"
$ git review
```
* The `git review` command output includes an URL for the change review. Example output:
```bash
$ git review
remote: Resolving deltas: 100% (1/1)
remote: Processing changes: new: 1, refs: 1, done
remote: (W) 5421c73: commit subject >65 characters; use shorter first paragraph
remote:
remote: New Changes:
remote:   https://review.openswitch.net/1148
```
 * In this example, the URL generated was: https://review.openswitch.net/1148
 * Add the document maintainers to the list of reviewers (see below)

### Adding the document maintainers to the list of reviewers for the change
* From the previous step, open the review URL and add the `doc-maintainers` group to the Reviewer List.
 * Type `doc-maintainers` in the text box and then click `Add Reviewer`.
 * The list will get filled with the names of the document maintainers.
* At this point the document has been submitted for  review and is now waiting for the community to review it.

### Responding to feedback and re-sending the document for review
If the change is not approved and further work is needed on the documents:
* Address the review comments.
* Run the following command to list all the documents that have been changed.
```bash
$ git status
```
* If the file listed in the output is ready to be submitted for review, run the following commands. It is the same command as the initial change but the commit command has an extra switch (`--amend`) that indicates that this is part of a previous change.
```bash
$ git add <filename>
$ git commit --amend -s -m "<a mandatory message with a description of the changes>"
$ git review
```

### After the document maintainers  have approved the changes
* Log in to the change review URL using the link provided by the `git review` command output.
* Add yourself as a reviewer.
* Add a comment to the change and give yourself a +1 rate.
* This initiates the process of merging your change with the main product documentation.
