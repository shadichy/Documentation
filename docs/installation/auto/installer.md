# Using the Installer

The new installer which is included within BlissOS bootable ISO is the new [aaropa](https://github.com/Ananda-Aropa)-based installer, that comes with a full graphical environment contains Calamares installer and a set of tools for debugging and diagnosing, completely leverage the user experience of Android-x86 installing procedure. This section will show you how to get BlissOS installed using the installer.

## Prerequisites

- An USB drive or an SD card (minimum 4Gb)

- A software to flash images to removable media (either [Rufus](https://rufus.ie/) (Windows) or [balenaEtcher](https://etcher.balena.io/))

- Free space on your hard drive (at least 16Gb of free storage)

Check out [hardware requirements](../../../knowledgebase/hardware-requirement) to know how much space you need. After knowing how much space you want to set, if you want to dual-boot with a existing operating system, we recommend splitting the drive directly on your current OS to create a new partition for BlissOS. This way you won't have to spend time with the partition tools anymore.

On Windows you can use third-party software or internal one like [Disk Management](https://learn.microsoft.com/en-us/windows-server/storage/disk-management/overview-of-disk-management).

!!!danger

    Do not trying to install the OS on an existing partition just so you don't have to split the drive. We do not recommend this practice !

## Flash the images

Download the iso at [https://blissos.org](https://blissos.org) and your preferred media flashing tool. Please refer to the following for which tool you are going to use:

### Using balenaEtcher

Open Etcher, choose the iso in `Flash from file `

![](../../../assets/etcher_01.png)

Plug your USB or SD Card in and choose it at `Select target`

![](../../../assets/etcher_02.png)

Then click `Flash!` to flash the image.

![](../../../assets/etcher_03.png)

Once done, congrats, you now have a bootable BlissOS drive 🥳

### Using Rufus

Open Rufus, plug your USB or SD Card in and select it

![](../../../assets/etcher_01.png)

Select the ISO

![](../../../assets/etcher_02.png)

Set mode to dd mode

![](../../../assets/etcher_03.png)

Then click `Flash!` to flash the image.

![](../../../assets/etcher_03.png)

Once done, congrats, you now have a bootable BlissOS drive 🥳

## Boot into the installer

!!!warning

    BlissOS doesn't support secure boot, if you are on uEFI and your secure boot is on, please disable it to be able to continue the installation !

Access the boot menu on your device, choose the flashed BlissOS drive. You'll see the boot loader menu appear:

![](../../../assets/etcher_03.png)

Move down and select `BlissOS installer` and you will enter the installation environment

## Getting Started with the installation environment

The installation environment is a minimal Linux environment that serves Calamares installer with a set of necessary tools to debug and diagnose the installation. From here, you can install, update, fix your BlissOS instance with guided instructions.

Here is a screenshot showing details about the installation environment:

![](../../../assets/etcher_03.png)

## Install BlissOS

### Select bootloader

As you entered the installation environment, a menu selecting bootloader will soon show up:

![](../../../assets/etcher_03.png)

We currently support within the installation process of 2 different bootloaders, you can choose one and continue:

- GRUB (recommended)
- rEFInd

If you don't want to install any bootloader to configure yourself, you can select `None`.

### Enter Calamares

The Calamares app will appear:

![](../../../assets/etcher_03.png)

### Partitioning

Click next, and the partition screen shall appear:

![](../../../assets/etcher_03.png)

There's 3 partitioning options available: `Install alongside`, `Replace a partition` and `Manual`.Please refer to the following for selecting the right option for you:

#### Install alongside

The program will automatically allocate a partition from free space and install BlissOS on it.
This option is recommended for most cases.

#### Replace a partition

Only choose this option once and only if you already cut a partition for BlissOS. 
Then pick the desired partition in the lower side (and optionally, select your preferred filesystem under the option) like below:

![](../../../assets/etcher_03.png)

And you're ready to go!

#### Manual

This is for people with decent knowings only. If you're not any familiar with Linux, you should pick the first 2 options. If you're already experienced using Calamares, please note that you're taking responsible for whatever happens with your machine once you choose to process on your own.

You should be navigated into this advanced partition dashboard like this below:

![](../../../assets/etcher_03.png)

The requirements of BlissOS (as mentioned above) include minimum 16Gb of storage, so you need to `Create` a partition with 16+Gb (any filesystem, but `ext4` is recommended) and mountpoint at `/`. 
You could also select the previously installed BlissOS partition and only set the mountpoint at `/`, without formatting (deselect `Format`) to upgrade your BlissOS instance without damaging userdata.

!!!warning

	A partition with mountpoint at `/` is required for BlissOS to process the installation.

If you want a separated userdata partition just go on with your desired partition with mountpoint at `/data`.

!!!warning

	If your machine is running on uEFI, you will need a 100+Mb FAT32 ESP partition too. If it already exists, select it, `Edit`, set mountpoint to `/boot/efi`, and **do not tick on formatting**. This is crucial because if you do, it will override all bootloader of other OS (Windows, Mac, Linux,...).

The final result should look like this:

![](../../../assets/etcher_03.png)

### Data image

After you've done previous step and navigating to the next step, a dialog will appear asking whether you want to create a data.img or not.

!!!info

	If you have no idea what this is about, just skip.

![](../../../assets/etcher_03.png)

This step allows you to customize the size of the data.img disk image. You can adjust the data.img size, or let the installer handle it.

![](../../../assets/etcher_03.png)

### Kernel argument options

!!!info

	Please refer to [BlissOS kernel arguments](/knowledgebase/kernel-arguments) for the information. If you have no idea what this is about, just skip.

You can expand and select (and edit) the kernel argument options. Each of the options indicates the kernel argument (with value) to be passed to the kernel when it is booting.

![](../../../assets/etcher_03.png)

### Summary

Once you've finished configuring the installation, click `Install` to begin the installation process.

![](../../../assets/etcher_03.png)

### Finished

Once the installation is complete, you can reboot into BlissOS.

![](../../../assets/etcher_03.png)

## Post-installation

