# Code Review

OpenSwitch runs [Gerrit](https://code.google.com/p/gerrit/) for online code reviews and [Git](http://git-scm.com/) as the distributed version control system.
The [OpenSwitch Review](https://review.openswitch.net/) site is the entry point for change, review, test and inclusion in one of the OpenSwitch projects.

## Contents
- [Setup the SSH key to work with the Review Server](#setup-the-ssh-key-to-work-with-the-review-server)
	- [Create the SSH keys](#create-the-ssh-keys)
	- [Add the SSH Public Key to the OpenSwitch Gerrit Review site](#add-the-ssh-public-key-to-the-openswitch-gerrit-review-site)
	- [Verify that an username is assigned to the Gerrit Review account](#verify-that-an-username-is-assigned-to-the-gerrit-review-account)
	- [Client SSH configuration](#client-ssh-configuration)
- [Working behind a firewall](#working-behind-a-firewall)
- [Configuring Git and Gerrit](#configuring-git-and-gerrit)
- [Installing git-review to work with the Review System](#installing-git-review-to-work-with-the-review-system)

## Setup the SSH key to work with the Review Server
In order to use the development environment you need to setup an SSH key with the Gerrit Review server at [review.openswitch.net](https://review.openswitch.net).

### Create the SSH keys
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

### Add the SSH Public Key to the OpenSwitch Gerrit Review site
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
 * OpenSwitch uses a `GitHub` account to login to the Review Site (Gerrit). If you do not have an account create one by cliking on `Sign up` in the GitHub page after you try to login to the [OpenSwitch Review](https://review.openswitch.net/).
 * Keep record of the password and username as this is your Gerrit user and is needed to work with OpenSwitch.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Select `SSH Public Keys` in the panel on the left.
* Paste the SSH public key (`id_rsa.pub` contents) in the input area for use with this site. The instructions for generating an SSH key are accessible just above the input area.
* Click `Add`

### Verify that an username is assigned to the Gerrit Review account
* Login to the [OpenSwitch Review](https://review.openswitch.net/) site by clicking on the Top Right `Sign in` link.
* Click on the `Settings` link (in the menu under the user's name in the upper right-hand corner).
* Click on the `Profile` link (left side)
* Verify that an username exists, or define one if it does not exist.
* Keep track of the username as this is the Gerrit User and will be used to work with OpenSwitch

### Client SSH configuration
* Add an entry similar to this to your `~/.ssh/config` file (preserve the indentation), which directs SSH to the proper SSH private key file to use for the OpenSwitch review website:
```bash
Host review.openswitch.net
  User <gerrit-user>
  IdentityFile ~/.ssh/id_rsa
```

## Working behind a firewall
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

## Configuring Git and Gerrit
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

## Installing git-review to work with the Review System
`git-review` is used to contribute changes back to the OpenSwitch project.

Install `git-review` as described [here](http://www.mediawiki.org/wiki/Gerrit/git-review), if not already installed on the development machine.

* Setup git-review
```bash
$ git-review -s
```
