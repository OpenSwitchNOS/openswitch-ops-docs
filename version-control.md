## Current build state
OpenSwitch has 2 primary branches:
* Release branch - Stable code but may not have the latest features. Current release branch name: REL_0.1.0
* Master branch - Source code with latest/current development activities

Each primary branch will have periodically generated tags to point to snapshot the repository at that time.

*Note - some repositories may also have feature branches where current development is done.*

### Stable vs. Latest
When you check out a repository, it will always contain the **latest** code.  When doing builds without checking out any repositories, it will contain the **stable** code for all repositories.  The stable version for each repository is stored in the bitbake files for that repository, which contains the git SHA for the last stable commit for that repository.

### Decoding Tags
* x.y.z - This type of tag refers to tags on master
* x.y.z_Ra.b - This type of tag refers to tags on the release branch

```ditaa
          0.1.0_R0.1
           +--------------------------------------------
           |                                         REL_0.1.0
           |
           |
           |
           |
    +------+--------------------------------------------
                                                     master
```

### Generating stable build
Bitbake recipes for each repository in ops-build are tagged to point to a stable version of the repository. To build a stable image for OpenSwitch:
Clone ops-build
```
git clone ops-build
```
Checkout the Release branch (REL_0.1.0) or any stable tag on the release branch. Currently the release branch has one tag: 0.1.0_R0.1
```git checkout REL_0.1.0```
to get the latest stable point on the release branch
```git checkout 0.1.0_R0.1```
to get to this specific stable point on the release branch

Build the stable image
```
make
```

### Generating latest build
To include the latest code of a feature repository in the build (for example 'ops-vland') you would need to checkout the source code of that module. Checking out the source code will always fetch the latest source code from the master branch.  The command to checkout repositories is `make devenv_add`.  This is fully documented here: [Changing OpenSwitch Code](http://openswitch.net/documents/dev/changing-openswitch-code).

Example of adding the 'ops-vland' repository:


```
make devenv_add ops-vland
```
Repeat the above step for all the necessary modules and then build the image.
```
make
```
NOTE: If a repository is removed using `make devenv_rm`, the subsequent make will include the stable version of the module as per the changeID in the bitbake recipe file for that module.
