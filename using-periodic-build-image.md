# Using an OpenSwitch Periodic Simulated Switch Image
The OpenSwitch infrastructure performs periodic builds and makes these images available to download. These builds include images for the AS5712 switch and for a "generic" (simulated switch). The periodic builds also include the Web UI component of OpenSwitch, which is not currently part of the normal developers' build.

Complete the sections in order. First create your own build and then use the periodic build, as described below.

## Cloning and building the code
Before setting up the periodic simulated switch image, follow the instructions in the [Step-by-Step Guide](http://www.openswitch.net/documents/dev/step-by-step-guide) to create the build yourself. Certain directories and files must exist before you can use the periodic build.

## Using the periodic build
After you build your simulated switch image, pull the periodic build and replace the image you built with the periodic build image, as described in the steps below.

### Removing the previous Docker image
Remove the Docker image you created from the [Step-by-Step Guide](http://www.openswitch.net/documents/dev/step-by-step-guide):
1. Shut down the existing Docker instance by entering the `shutdown now` command.
2. To remove the Docker instance, enter the following commands:
```
docker rm ops
docker rmi openswitch
```

### Downloading the latest periodic build
1. Browse to the following website to download the latest generic image:  [https://archive.openswitch.net/artifacts/periodic/latest/genericx86-64/](https://archive.openswitch.net/artifacts/periodic/latest/genericx86-64/)

2. Download `openswitch-disk-image-genericx86-64-<version/date>.tar.gz`. The filename is unique for each build. Download the file from the website or on your Linux development system, use the `wget` command:
```
wget https://archive.openswitch.net/artifacts/periodic/latest/genericx86-64/openswitch-disk-image-genericx86-64-<version/date>.tar.gz
```

### Replacing the built image with the periodic build image
Replace the current disk image with the one you downloaded. From the top-level directory where you cloned the code, enter the following commands from the top-level directory of your cloned repository:

```
cd build/tmp/deploy/images/genericx86-64
rm openswitch-disk-image-genericx86-64.tar.gz
cp <downloaded-file> openswitch-disk-image-genericx86-64.tar.gz
cd ../../../../..
```

### Exporting the Docker image

1. Export the updated disk image:
```
make export_docker_image openswitch
```
2. Follow the remaining instructions in the [Step-by-Step Guide](http://www.openswitch.net/documents/dev/step-by-step-guide).

## Using the web UI
After your new image is up and running, point a local web browser to the IP address of your simulated switch image.
Note: You must use the web browser from the host machine running the Docker image.

### Location of the web UI files
The web UI files can be found in the following directory on the switch:  `/srv/www/static`
