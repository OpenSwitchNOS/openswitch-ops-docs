# Getting Acquainted with OpenSwitch
This page provides a directory of tutorials that cover increasingly involved aspects of OpenSwitch. To have a software image to run only requires completing the first step; complete additional steps to become a participating member of the OpenSwitch community.

## Contents
- [Getting Started](#Getting-Started)
- [Modifying the Existing Code Base](#Modifying-the-Existing-Code-Base)
- [Debugging an Image](#Debugging-an-Image-)
- [Contributing Changes Upstream to OpenSwitch](#Contributing-Changes-Upstream-to-OpenSwitch)
- [Adding New Code to OpenSwitch](#Adding-New-Code-to-OpenSwitch)

## Getting Started

The [Getting Started with OpenSwitch](./getting-started.html) provides the steps to setup a suitable environment for working with OpenSwitch, obtaining the source, and configuring and building an image for a switch.
## Modifying the Existing Code Base

The [Development Environment](./dev-env.html) guides describes the options for making modifications to the existing code base using the built-in support around the Yocto project's `devtool`.

Edit, build, test cycles can be done quickly by [booting an image with NFS](./dev-env.html#how-to-setup-an-nfs-root-environment-for-development).

## Debugging an Image
Occasionally, software requires debugging. OpenSwitch supports [debugging an image](./debug-how.html) with symbols both on and off target hardware.

## Contributing Changes Upstream to OpenSwitch
When changes are ready for inclusion in the OpenSwitch code base, they are [submitted for review](./contrib-code.html) and automated testing.

## Adding New Code to OpenSwitch
OpenSwitch supports multiple software images and features. Customization of the image can be done to support new machine architectures or different feature sets. [How to add new code to OpenSwitch](./dev-env.html#how-to-add-new-code-to-openswitch) describes how to add recipes to include additional software features in an image.
