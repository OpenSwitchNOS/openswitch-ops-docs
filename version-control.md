## Current build state
OpenSwitch has 2 branch types.
* Release branch - Stable code but may not have the latest features. Current release branch name: REL_0.1.0
* Master branch - Source code with latest/current development activities

Each branch will have periodically generated tags to point to snapshot the repository at that time.
### Decoding Tags
* x.y.z - This type of tag refers to tags on master
* x.y.z_Ra.b - This type of tag refers to tags on the release branch

### Generating stable build
Bitbake recipes for each repository in ops-build are tagged to point to a stable version of the repository. To build a stable image for OpenSwitch:
Clone ops-build
```
git clone ops-build
```
Checkout the Release branch (REL_0.1.0) or any stable tag on the release branch. Currently the release branch has 1 tag 0.1.0_R0.1
```
git checkout REL_0.1.0 (OR)
git checkout 0.1.0_R0.1
```
Build the stable image
```
make
```

### Generating latest build
To include the latest code of a repository in the build e.g. ops-vland:
Checkout the source code of that module. Checking out the source code will always fetch the latest source code from the master branch.
```
make devenv_add ops-vland
```
Repeat the above step for all the necessary modules and then build the image.
```
make
```
NOTE: If a repository is removed using devenv_rm, the subsequent make will include the stable version of the module as per the changeID in the bitbake recipe file for that module.
