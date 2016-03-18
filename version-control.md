## Current build state

Builds on OpenSwitch is controlled and managed within the ops-build repository using the yocto bitbake recipe files.

All OpenSwitch has 2 primary branches:
- Release branch (branch name: release) which contains stable code but may not have the latest features.
- Master branch (branch name: master) which has the most recent development code.

The versioning and tagging of builds is managed using tags in ops-build repository.
The release branch will have periodically generated tags to snapshot the software at a given time.

*Note - Code in master branches are not tagged. *

The periodicity is normally set to 3 weeks where the patch number increses and minor number increases once every 13 weeks.
Note that the above periodicity is subject to change based on decisions made by the program management team.
Please contact Barbara Crosser (barbara.crosser at hpe dot com) for more details

*Note - some repositories may also have feature branches where current development is done.*

### Stable vs. Latest
All the OpenSwitch recipes in the ops-build directory contain a fixed SHA which points to the stable point in the corresponding repository. When an image is compiled without cloning any other repository other than ops-build, the image contains the code corresponding to the respective SHA and hence a **stable** version for each module. However, if any repository is cloned, the code that is cloned is at the HEAD of the repository and the subsequent compilations will have the **latest** code included in the package.

### Decoding Tags
Versioning for OpenSwitch follows semantic versioning.

* x.y.z - This type of tag refers to tags on release branch where
** x - Major Version (increases when backward compatibility is broken)
** y - Minor Version (increases approximately once every 13 weeks)
** z - Patch Number (increases approximately once every 3 weeks)

```ditaa
                    0.2.0      0.2.1            0.3.0
           +----------+----------+----------+-----+-----
           |                                |       release
           |                                |
           |                                |
           |                                |
           |                                |
    +---+--+-----+--------------------------+-----------
    0.2.0(dev) 0.3.0(dev)                           master
```

### Generating stable build
Bitbake recipes for each repository in ops-build are tagged to point to a stable version of the repository. To build a stable image for OpenSwitch:
Clone ops-build
```
git clone ops-build
```
Checkout the Release branch (release) or any stable tag on the release branch.
```git checkout release```
to get the latest stable point on the release branch
```git checkout <tag>```
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
