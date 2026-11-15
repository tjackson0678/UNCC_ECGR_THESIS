## CESP 3.1 Gryphon_HT Linux Build and Flash guide

### Table of Contents
 
<!-- omit in toc -->  


- [Table of Contents](#table-of-contents)
- [Overview](#overview)
- [Build Machine Setup](#build-machine-setup)
- [Build Machine configuration (Host Development System)](#build-machine-configuration-host-development-system)
- [Required Host Packages](#required-host-packages)
- [Set locale](#set-locale)
- [Setting up Repo utility](#setting-up-repo-utility)
- [Adding ~/bin directory to your path](#adding-bin-directory-to-your-path)
- [Adding repo to the PATH](#adding-repo-to-the-path)
- [Preparing to build](#preparing-to-build)
- [QT6 Build Configuration](#qt6-build-configuration)
- [Prerequisite for cloning from GitHub](#prerequisite-for-cloning-from-github)
- [Generate SSH Key](#generate-ssh-key)
- [Cloning the manifest file](#cloning-the-manifest-file)
- [Setting up the environment \& build](#setting-up-the-environment--build)
- [Bitbake options](#bitbake-options)
- [Restarting a build environment](#restarting-a-build-environment)
- [Multilib enablement](#multilib-enablement)
- [BitBake logs](#bitbake-logs)
- [Customization](#customization)
- [Creating a custom distro](#creating-a-custom-distro)
- [Select additional packages](#select-additional-packages)
- [Package group](#package-group)
- [Toolchain Configuration](#toolchain-configuration)
- [Kernel Module Compilation](#kernel-module-compilation)
- [Instructions for Flashing SRK Hash into SOC fuses](#instructions-for-flashing-srk-hash-into-soc-fuses)
- [Image Flashing \& PSLC mode setting on EMMC](#image-flashing--pslc-mode-setting-on-emmc)
- [Flashing image on Gryphon hardware for SD-boot](#flashing-image-on-gryphon-hardware-for-sd-boot)
- [References](#references)
- [Docker](#docker)

### Overview

This document describes how to build an image for an i.MX board by using a Yocto Project build environment with mender features included.

The Yocto Project is an open-source collaboration focused on embedded Linux OS development. Files used to build an image are stored in layers. Layers contain different types of customizations and come from different sources. Some of the files in a layer are called recipes. Yocto Project recipes contain the mechanism to retrieve source code, build and package a component.

The contents of the CESP BSP Release layers are recipes and machine configurations. MetaCarrier layer implements recipes or include files which provides updates to the recipes by either appending to a current recipe or including a component and patch updates.

All packages that are released as binary are built with hardware floating point enabled as specified by the DEFAULTTUNE defined in each machine configuration file.

CESP Linux is released for Yocto Project 5.0 (Scarthgap), Linux Kernel 6.6.112 and U-boot 2024.04

The following lists show the layers used in this release

| #   | Layer                          | Description                                                                                 | Link                                                                           |
| --- | ------------------------------ | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| 1   | poky                           | Basic Yocto Project items in Poky                                                           | https://git.yoctoproject.org                                                   |
| 2   | meta-carrier                   | BSP and machine configurations                                                              | [meta-carrier](https://github.com/Carrier-CESP/meta-carrier)                   |
| 3   | meta-packages                  | Provides Middleware packages                                                                | [meta-packages](https://github.com/Carrier-CESP/meta-packages)                 |
| 4   | meta-qt6                       | Provides Qt 6.5.7 LTS                                                                       | https://code.qt.io/yocto                                                       |
| 5   | meta-arm                       | Layer containing support for Arm products                                                   | https://git.yoctoproject.org                                                   |
| 6   | meta-freescale                 | Provides support for the base and for Arm reference boards. i.MX                         | [Freescale](https://github.com/Freescale)                                      |
| 7   | meta-freescale-3rdparty        | Provides support for 3rd party and partner  boards                                       | [Freescale](https://github.com/Freescale)                                      |
| 8   | metafreescale-distro           | Additionalitems to aid in development and exercise board capabilities.                   | [Freescale](https://github.com/Freescale)                                      |
| 9   | fsl-community                  | BSP base files                                                                              | [Freescale](https://github.com/Freescale)                                      |
| 10  | meta-imx                       | Yocto Project BSP Layer i.MX                                                                | [nxp-imx](https://github.com/nxp-imx)                                          |
| 11  | meta-browser                   | Provides several browsers                                                                   | [O.S.Systems Software LTDA](https://github.com/OSSystems)                      |
| 12  | meta-security                  | Security tools for Internet-connected devices                                            | https://git.yoctoproject.org                                                   |
| 13  | meta-virtualization            | Layer enabling hypervisor,virtualizationtool stack,and cloud support.                   | https://git.yoctoproject.org                                                   |
| 14  | meta-nxp-demo-experience #     | Launcher application for GoPoint for Applications Processors i.MX                        | [NXPi.MX Support](https://github.com/nxp-imx-support)                          |
| 15  | meta-openembedded              | Collection of layers forthe OE-core universe.                                           | [openembedded](https://github.com/openembedded)                                |
| 16  | meta-clang                     | Clang C/C++cross compiler and runtime for OpenEmbedded/Yocto Project                    | [kraj - Overview](https://github.com/kraj)                                     |
| 17  | meta-carrier-distro            | Provides Distro configurations                                                              | [meta-carrier-distro](https://github.com/Carrier-CESP/meta-carrier-distro)     |
| 18  | carrier-manifests              | Manifest file to fetch meta-layers                                                          | [carrier-manifests](https://github.com/Carrier-CESP/carrier-manifests)         |
| 19  | meta-timesys-security          | Provides Vigiles tools for monitoring and notification of BSP vulnerabilities (CVEs) | [meta-timesys-security](https://github.com/Carrier-CESP/meta-timesys-security) |
| 20  | Mender                         | Mender related support                                                                      | [Mender](https://github.com/mender)                                            |

`#` - Not included as part of RFS / Image

### Build Machine Setup

### Build Machine configuration (Host Development System)

Depending on the number of processors and cores, the amount of RAM, the speed of the Internet connection and other factors, the build process could take several hours the first time it is run. Subsequent builds run much faster since parts of the build are cached.

- Minimum hard disk size of 500 GB

- Minimum ram of 16 GB

- Ubuntu 22.04

- For one build minimum of 160GB is required.

### Required Host Packages

Run command below to install essential Yocto Project host packages

```
1 $ sudo apt install gawk wget git diffstat unzip texinfo gcc Abuild-essential chrpath socat cpio \
2 $ python3 python3-pip python3-pexpect xz-utils debianutils \
3 $ iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \
4 $ python3-subunit mesa-common-dev zstd liblz4-tool file locales -y
```

### Set locale

```
1 $ sudo locale-gen en_US.UTF-8
2 $ export LC_ALL="en_US.UTF-8"
3 $ export LC_CTYPE="en_US.UTF-8"
```

### Setting up Repo utility

Repo is a tool built on top of Git that makes it easier to manage projects that contain multiple repositories, which do not need to be on the same server. Repo complements very well the layered nature of the Yocto Project, making it easier for users to add their own layers to the BSP

To install the “repo” utility, perform these steps. Ignore if repo utility already present.

Create a bin folder in the home directory.

```
$ mkdir ~/bin
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

### Adding ~/bin directory to your path

Temporary:

```
- 1 `$ export PATH=/home/YOURUSERNAME/bin:$PATH `

- 2 $ source .bashrc
```

### Adding repo to the PATH

Edit the profile file of the user and add the below snippet at the end of the file

```
- 1 if [ -d “$HOME”/bin ]; then 2 PATH=$PATH:$HOME/bin 3 fi
```

### Preparing to build

### QT6 Build Configuration

Note: QT compilation requires a license and user credentials to clone the source.

Perform the following steps on the build machine for QT6 compilation.

1. Get the .netrc & .qt-license files

2. Copy the files into ~/ (home directory)

### Prerequisite for cloning from GitHub

**Note:** Skip this section if not using GitHub, execute similar instructions for the target source control

### Generate SSH Key

1. Open a terminal.

2. Generate SSH keypair using the command:

3. ssh-keygen -t rsa -b 4096 -C your_email@example.com

4. Confirm the location to save the key (default is ~/.ssh/id_rsa)

5. **Note:** Selecting the same location will overwrite any existing key pair.

6. Ignore and proceed without an optional passphrase to protect the private key.

7. After completing the prompts, a default keypair will be generated. \
   a. Private key: Stored in ~/.ssh/id_rsa \
   b. Public key: Stored in ~/.ssh/id_rsa.pub \
   c. Open the id_rsa.pub and copy the key for the next step.

Copy SSH key to GitHub user-settings

To clone from the required repositories from Carrier-CESP GitHub organization, the ssh

public key of the host (development machine) should be added under the section “SSH and GPG keys” in the user-settings. Here is the link to settings: SSH and GPG keys.

After adding the SSH key, also configure SSO for the key.

### Cloning the manifest file

Firstly, make sure that Git is set up properly with the commands below:

```
1 $ git config --global user.name "Your Name"
2 $ git config --global user.email "Your Email"
3 $ git config --list
```

Run the following commands to clone the repositories.

```
1 $ mkdir cesp-yocto-bsp
2 $ cd cesp-yocto-bsp
3 $ # repo init -u git@github.com:Carrier-CESP/carrier-manifests -b scarthgap -m "cesp3.1/cesp-3.1-c.xml"
4 $ repo sync
```

**_Note:_** Modify the git URL to point to the BU source control repository

With the above commands, the yocto layers are cloned into the sources folder.

```
Note:

Update Manifest remote URL

 - After repo init update fetch url of carrier remote in cesp-3.1-c.xml(.repo/manifests/cesp-3.1)

<remote name="carrier" fetch="ssh://git@ssh.dev.azure.com/v3/carriercorp/Gryphon"/>
```

### Setting up the environment & build

```
1 # MACHINE=imx8mp-ht-ecs DISTRO=chvac source sources/meta-carrier-distro/conf/carrierconf/carrier-imx-setup-release.sh -b build
```

```
 As default image is built for MLC eMMC, to build for TLC eMMC follow below steps.

1. Update machine configuration file to use Ss wks.in as WKS_FILE

   1 vi /sources/meta-carrier/conf/machine/imx8mp-ht-ecs.conf
   2
   3 # Partition layout definition for 32GB MLC eMMC
   4 # WKS_FILE = "imx8mp-ht-ecs-32mlc-2MB-aligned.wks.in"
   5
   6 # Partition layout definition for 32GB TLC eMMC
   7 WKS_FILE = "imx8mp-ht-ecs-32tlc-2MB-aligned.wks.in"


2. Update the mender configurations in cesp-mender.conf file as follows


   1 vi /sources/meta-carrier-distro/conf/carrier-conf/imx8mp-ht-ecs/cesp-mender.conf
   2
   3 # Sizes as per 32GB TLC eMMC
   4 MENDER_STORAGE_TOTAL_SIZE_MB = "9660"
   5 MENDER_DATA_PART_SIZE_MB = "2048"
   6
   7 # Sizes as per 32GB MLC eMMC
   8 # MENDER_STORAGE_TOTAL_SIZE_MB = "14168"
   9 # MENDER_DATA_PART_SIZE_MB = "3380"
```

```
As the default image is built for emmc boot, for sd boot, please follow the below command


1 MACHINE=imx8mp-ht-ecs DISTRO=chvac BOOT_DEVICE=sd source sources/meta-carrierdistro/conf/carrier-conf/carrier-imx-setup-release.sh -b build
```

The above command creates the folder build.

```
Note:

   Update Platform Git URI

   - After build setup add variable PLATFORM_GIT_URI in local.conf file
     (build/conf/loc``al.conf).

     PLATFORM_GIT_URI = git://git@ssh.dev.azure.com/v3/carriercorp/Gryphon

or

Run the following command

# echo "PLATFORM_GIT_URI = \"git://git@ssh.dev.azure.com/v3/carriercorp/Gryphon\"" >> conf/local.conf
```

Run the command below to start compilation

```
1 Bitbake command for Prod Image
2 # bitbake cesp-image-prod
3
4 Bitbake command for Test Image
5 # bitbake cesp-image-test
```

Post Bitbake, the Images are created and are available in the folder tmp/deploy/images/${machine}

Generate SDK to create cross-compile toolchain.

```
1 Bitbake command to generate toolchain from prod image
2 # bitbake cesp-image-prod -c populate_sdk
3
4 Bitbake command to generate toolchain from test image
5 # bitbake cesp-image-test -c populate_sdk
```

Post Bitbake, the Images are created and are available in the folder tmp/deploy/sdk

**_Note:_** If the building process is interrupted, modify conf/local.conf to comment out the line:

PACKAGE_CLASSES = "package_deb", and then execute the populate_sdk command again

### Bitbake options

The bitbake command used to build an image is bitbake <image name>. Additional parameters can be used

for specific activities described below. Bitbake provides various useful options for developing as a single component.

The following table provides some BitBake options.

BitBake parameter   |  Description
| ----------------- | ------------------------------------- | 
| -c fetch          | Fetches if the downloads state is not marked as done.                                                                                                                                                                       
| -c cleanall       | Cleans the entire component build directory. All the changes in the build directory are lost. The rootfs and state of the component are also cleared.The componentis also removed from the download directory.                            |
| -c deploy         | Deploys animage or component to the rootfs.                                                                                                                                                                                                           |
| -k            | Continues building components even if a build break occurs.                                                                                                                                                                                            |
| -c compile -f | Itis not recommended that the source code under the temporary directoryis changed directly,butifit is,the Yocto Project might not rebuild it unless this optionis used. Use this option to force a recompile after theimageis deployed. |
| -g            | Lists a dependency tree for an image or component.                                                                                                                                                                                                    |
| -DDD          | Turns on debug 3 levels deep.Each D adds another level of debug.                                                                                                                                                                                      |
| -s            | Shows the current and preferred versions of all recipes                                                                                                                                                                                               |

### Restarting a build environment

If a new terminal window is opened or the machine is rebooted after a build directory is set up, the setup environment script should be used to set up the environment variables and run a build again.

The full imx -setup-release.sh is not needed.

```
1 $ source setup-environment <build-dir>
```

### Multilib enablement

For i.MX 8, building 32-bit applications on 64-bit OS can be supported using the multilib configuration. Multilib offers the ability to build libraries with different target optimizations or architecture formats and combine these together into one system image.

Multilib is enabled by adding the MULTILIB, DEFAULTTUNE, and IMAGE_INSTALL declaration to your local.conf file. Multilib is not supported with the debian package management. It requires the RPM system. Comment out the two package management lines in local.conf to go to the default RPM

The MULTILIBS declaration is typically lib32 or lib64 and needs to be defined in MULTILIB_GLOBAL_VARIANTS variable as follows:

```
- 1 MULTILIBS = "multilib:lib32"
```

DEFAULTTUNE must be one of the AVAILTUNES values for this alternative library type as follows

```
- 1 DEFAULTTUNE:virtclass-multilib-lib32 = "armv7athf-neon"
```

### BitBake logs

BitBake logs the build and package processes in the temp directory in tmp/work// /temp. If a component fails to fetch a package, the log showing the errors is in the file log.do_fetch. If a component fails to compile, the log showing the errors is in the file log.do_compile.

Sometimes a component does not deploy as expected. Check the directories under the build component directory (tmp/work//). Check the package, packages-split, and sysroot\* directories of each recipe to see if the files are placed there (where they are staged prior to being copied to the deploy directory).

### Customization

Customizing a distribution adding or removing packaging from the BSP provided for CESP Linux releases by creating a custom Yocto Project layer. The section provides instructions for creating a custom DISTRO and board configuration.

### Creating a custom distro

Custom distro can configure a custom build environment. The distro file cesp-image-test shows configuration for specific board.

Distros can also be used to configure other parameters such as kernel, U-Boot. The CESP distro files are set to create a custom build environment required for compiling CESP Linux OS BSP releases.

A distro is created by copying an existing distro file or including one like chvac.conf and adding additional changes or including one of the i.MX distros and using that as a starting point.

### Select additional packages

An image is a set of packages and the environment configuration

- An image file (such as cesp image test.bb) defines the packages that go inside the file system.

Additional Packages can be included in the Image by creating packagegroups

### Package group

A package group is a set of packages that can be included on any image. A package group can contain a set of packages.

Additional packages can be installed by adding the following line in <build_dir>/local.conf.

```
1 CORE_IMAGE_EXTRA_INSTALL:append = " <package_name1 package_name2>"
```

### Toolchain Configuration

From the build directory, copy the sh file in tmp/deploy/sdk to the host machine to build on and execute the script to install the SDK. The default location is in /opt, but it can be placed anywhere on the host machine.

set the environment with the following command to cross compile an application binary or Kernel Module

```
1 source <PATH_TO_TOOLCHAIN>/environment-setup-armv8a-poky-linux export ARCH=arm64
```

Once the environment is sourced, compilation can be initiated using Make or CMake.

### Kernel Module Compilation

The Kernel Headers are included as part of the cross-compiler toolchain. The K Headers are in the path: <PATH_TO_TOOLCHAIN>/sysroots/armv8a-poky-

linux/lib/modules/<kernel_version>/build

Add below configuration if no Kernel Headers are included in the SDK and rerun the bitbake:

meta-carrier/recipes-core/images/cesp-image-minimal.inc

```
1 TOOLCHAIN_TARGET_TASK += " kernel-devsrc"
```

Steps to compile a Kernel Module:

1. Source the environment if not already done

2. Goto kernel headers build folder

3. Run command: make modules_prepare

4. Modify the KDIR and CROSS_COMPILE variables in the Module Makefile

```
1 KDIR := <PATH_TO_TOOLCHAIN>/sysroots/armv8a-poky-linux/lib/modules/<kernel_version>/build 
2 CROSS_COMPILE := <PATH_TO_TOOLCHAIN>/systoots/x86_64-pokysdk-linux/usr/bin/aarch64-pokylinux/aarch64-poky-linux-
```

### Instructions for Flashing SRK Hash into SOC fuses

***Important Notice: One-Time SRK Hash Fuse Programming***

***Note:*** The Images are signed with developer keys as part of Yocto build process

Be extremely careful when writing the SRK (Super Root Key) Hash to the device fuses.

Once the fuses are burned with an SRK Hash, they cannot be changed or overwritten with another SRK Hash on the same board.

Download below Gryphon-HT SRK HASH Flashing Tool

SRK_HASH_Fu… \_HT.zip 06 Mar 2026, 03:32 PM

Put the device into Flashing Mode.

- Run fuse_srk_hashes_uuu.bat file shown below
```
1 ./fuse-srk-hash-flashing-imx8mp-ht.bat
```
- At the prompt , choose the option to program Development or Production SRK Hashes

After flashing, reboot the device, connect to the serial console, stop at the U-Boot prompt, and

run below command to verify if the SRK Hash key was successfully flashed.
```
1 fuse read 6 0 4 
2 fuse read 7 0 4
```
SRK HASH of DEV Keys

### Image Flashing & PSLC mode setting on EMMC

PSLC mode setting on MLC eMMC and Flashing image using UUU tool CESP-3.1

### Flashing image on Gryphon hardware for SD-boot

Note: Ignore this section if Image not built with Mender Integration changes

1. Keep device boot pins in Force recovery Mode.

2. Connect device through USB(3.0 recommended) as shown below and check if device is listed as shown in Fig: 3.3.2.1 in uuu tool through below command
```
1 uuu -lsusb
```

3. Steps to flash SD Card

Prerequisite: Download all the files into single folder

Flash the bootloader (without mender)

```
1 uuu -b sd_all signed-imx-boot-imx8mp-ht-ecs-stock.bin-flash_evk
```

Flash the WIC.ZST using the bootloader without mender (from the above step). Select One of the below commands depending on the choice of image to flash.

   Prod Image:

```
1 uuu -b sd_all signed-imx-boot-imx8mp-ht-ecs-stock.bin-flash_evk cesp-image-prod-imx8mp-htecs.wic.zst
```

   Test Image:

```
1 uuu -b sd_all signed-imx-boot-imx8mp-ht-ecs-stock.bin-flash_evk cesp-image-test-imx8mp-htecs.wic.zst
```

Run mender.auto file, which is provided in artifacts, with uuu tool.

```
1 uuu mender_sd_boot.auto
```

After flash is successful switch boot pins to SD mode and power on the device

### References

[i.MX Linux User's Guide]()

[i.MX Yocto Project User's Guide]()

[Yocto Project Reference Manual — The Yocto Project ® 5.0.7 documentation]()

### Docker

[i.MX]() recently released support for docker build setup which is yet to be supported for CESP Linux and shall be taken up in the upcoming release.

Users can familiarize the docker build instructions in the following link:

[nxp-imx/imx docker: i.MX Docker]()

Branch: imx-6.6.52-2.2.0


[def]: #setting-up-the-environment--build
[def2]: #cesp-31-gryphon_ht-linux-build-and-flash-guide
[def3]: #table-of-contents
