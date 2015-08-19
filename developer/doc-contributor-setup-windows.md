---
title: Configuring Windows
layout: document
---
# Configuring Windows

## Clarification
If you want to contribute or build the project, you have to check the following link [Getting Started with OpenSwitch](../getting-started). In Windows you are not able to build the system unless you have a virtual machine; however, you can access and edit all the documentation.

## System Requirements
Before you start working in windows you need to configure a set of tools.
* Text Editor.
* Git.
* Install ssh-keys.
* Install socat.
* Install git review for windows

## Text Editor
All the documentation in the project has been written in markdown, our recommendation is using a text editor with the support for it, you can install Atom.
* The first step is download the text editor in the following url [Atom.io](https://atom.io/).
* You can see a red button “Download Windows Installer”, you have to press it and wait for a while.
* After the PC finish to download the software, you have to install it; Internet Explorer will ask you if you want to run the installer and you will select “yes” and wait for while, this process will take a couple of minutes, then you will see the main screen of Atom.

**Note**: You can get more information about Atom in following link [getting-started-why-atom](https://atom.io/docs/v1.0.2/getting-started-why-atom).

## Installing Git
* Download msysGit package in the following link [msysGit](http://msysgit.github.io/).
* Once you have downloaded the msysGit executable
  * Double click on it to start the installation wizard. Leave the default directory options.
  * When you get to the “Adjusting your Path environment” setting, select the “Run Git from the Windows Command Prompt” option. Choosing this option will make it easy for you to run Git commands from the Windows Command Prompt (command line) if you choose.

**Note**: Command Prompt is a simple tool, where you can run commands, switch through folders, manage files and it can be ran by selecting RUN… in START menu, and executing cmd command.

## Installing SSH Keys
We need to generate ssh keys, so please follow the next steps:
* Please press right clic in you desktop.
* You will see a menu, please select Git bash, this action is going to open a new window prompt.
* In the prompt type ssh-keygen.
* After that, the machine show some question like “Enter same passphrase” just press Enter in all of them.
* You will have your ssh keys in c:\Users\username\.ssh\id_rsa.pub
* Is necessary to have an account in the site [ubuntu-one](https://login.ubuntu.com/+login); if you don’t have it, please create one using your hp account.
* After that you need to go to [Gerrit](https://review.openhalon.io/#/)  and sign in with the ubuntu one account

## Uploading the ssh key
You need to upload the ssh key into [Gerrit](https://review.openhalon.io/#/), please follow the next steps
* Go to the c:\Users\username\.ssh\ and open the file id_rsa.pub
* Also Go to the following url [keys](https://review.openhalon.io/#/settings/ssh-keys)
* From the file id_rsa.pub (it is a key) copy all the information, it has to be something similar to this:
````bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDGIAx8Ko3RhMmUY15NXiFH/fCX2PYAITMBB0aZuUhxp3vQbFZSuWdTlTfDSpmF1BMY4YFseyyBYS5yacMVCp+4NmttJa4BUeKoJLihpr/Khtdasdax0kIkc4WBGjwSYOn3QyHeFQCMDZfFVFLMnYj7VZP6JYYqEYQQs0U+CI2Bx7kDr/zX8RAnTv18Q+2DDgUU+Pew14CNszmc8YR0zZrwKOasdasdHCenZWs5yKln0/6O2qJFEkkXepY+JiNqTaZHyPp8pU4Oh2cXUrGQX5Kz+2IryAY2no5EShRUv/dTf5+q+jRHvbkpMDefaz/0jtF5nGh4I5coCOso7oi+GduAasV7 user@machine
````
* In the website press “Add Key” and paste the key in the dialog box then press Add

## Install socat
* Download and setup socat for Windows on [Socat](http://blog.gentilkiwi.com/downloads/socat-1.7.2.1.zip)
* Create c:\socat directory and extract the zip files under it.

## Configuration of the proxy
If you are inside the HP network you need to config the proxy
* Go to the c:\Users\username\.ssh\ and open the file config
* You have to add the following information:
````bash
Host review.openhalon.io
    User your-user
    IdentityFile C:\Users\username\.ssh\id_rsa
    ProxyCommand C:\\socat\\socat.exe - PROXY:web-proxy.americas.hpqcorp.net:%h:%p,proxyport=8080
````

**Note**: The last step is just because, you are inside a firewall, when the project will set like a open source you can omit this step.

## Checking the connection
Before you do this step, you have to have completed the previous steps
* In your desktop do a right clic -> Git Bash, this command will open a terminal window
* In the terminal window type ssh user@review.openhalon.io -p 29418 and press enter
* The program will ask “Are you sure you want to continue connecting” -> please type “yes”
* You will see the following message: "Welcome to Gerrit Code Review ..."

## Git Review
Python is needed for git-review to function and pip is used for its installation:
* Install Python or upgrade to most current version (either 3.4.2 or 2.7.8 depending on your python installation).
* Important: Do not install Python in any directory with a space in its path as there is a pip bug, use the default, e.g.
````bash
C:\Python34\
````
* During the Python installation, manually select Add python.exe to Path
* Otherwise, add your python scripts directory manually to the system path (Settings, Control panel, System, Advanced system settings, Environment variables, User variables, Path; e.g. C:\Python34\;C:\Python34\Scripts\;). Different directories in path are delimited by a semicolon ";" only - do not add any whitespace to path list.)
* Python 3.4 has pip already installed. Only if you have an older version, install pip by following the instructions here.
* Run Git Bash as Administrator (right click on icon for this option) and install git-review with the following command:
````bash
$ pip install git-review
````
To use git review, you have to be in a git clone directory that already contains a (possibly hidden) .gitreview configuration file, otherwise you will get the error message UnboundLocalError: local variable 'no_git_dir' referenced before assignment

## Configuring git-review
You need to do a small configuration for git-review, so you need to look for this file  %USERPROFILE%\.config\git-review\git-review.conf) and add these two lines:
````bash
[gerrit]
defaultremote = origin
````
## Cloning the website
* Please in your terminal type the following command git clone
````bash
ssh://youruser@review.openhalon.io:29418/openswitch/docs
````
* This command will clone the website repository in your Desktop

## Setting up git-review
After clone a repository, you need to set it up for git-review. This will automatically happen the first time you try to submit a commit, but it's generally better to do it right after cloning. In your project's directory ("examples"), enter
````bash
$ git review -s
````
You will see a message "The authenticity of host gerrit.openswitch.net can't be established..." you have to type yes in the question "Are you sure you want to continue connection (yes/no)"
With these steps you will be able to send a review into the server
