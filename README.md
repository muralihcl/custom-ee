# Create Custom Execution Environment container image for Ascender

To create a container image and include Python 3 is needed. Not many of the base container images have Python3 installed. Whichever base image that needs to be used, check for availability of Python 3 package installed in it. one of the selected container images is from ```quay.io/centos/centos:stream9```. If any other image can be found with this, it can be used as well.

Clone the ```custom-ee``` repository into a folder.

```bash
# git clone https://github.com/muralihcl/custom-ee.git
```

Create a python3 virtual envirionment and install ansible-builder. It would be better if the below steps are executed as root.

```bash
# python3 -m venv virtualenv
# source virtualenv/bin/activate
( virtualenv ) # pip install --upgrade pip
( virtualenv ) # pip install setuptools
( virtualenv ) # pip install ansible-builder
```

Install podman that can be used as a default container runtime
```bash
# dnf install podman -y
```

Build the image using ansible-builder. The -v option is to include the verbosity level. -t is to specify the tag.
```bash
( virtualenv ) # cd custom-ee
( virtualenv ) # ansible-builder build -v3 -t ghcr.io/ctrliq/custom-ee:latest
```

Even after building the image, the image would not be accessible to the Asender. So, we need to save the tar file from podman images list and then import it using ctr utility. Using crictl utility, check if the image ```ghcr.io/ctrliq/custom-ee:latest``` is visible in the output.

```bash
( virtualenv ) # podman save -o custom-ee.tar ghcr.io/ctrliq/custom-ee:latest
( virtualenv ) # ctr image import custom-ee.tar
( virtualenv ) # crictl image ls
```

This image now can be used as a custom Execution Environment from within Ascender. As it is being used from the local system (not online), ensure the image pull policy is set to never.
