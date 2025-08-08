# Using the Bootable Installer

The new installer which is included within BlissOS bootable ISO is the new [aaropa](https://github.com/Ananda-Aropa)-based installer, that comes with a full graphical environment contains [Calamares installer](https://calamares.io/) and a set of tools for debugging and diagnosing, completely leverage the user experience of Android-x86 installing procedure. This section will show you how to get BlissOS installed using this bootable installer.

## Prerequisites

- An USB drive or an SD card (minimum 4GB)

- A software to flash images to removable media (either [Rufus](https://rufus.ie/) (Windows) or [balenaEtcher](https://etcher.balena.io/))

Check out [hardware requirements](../../../knowledgebase/hardware-requirement) to know how much space you need. After knowing how much space you want to set, if you want to dual-boot with a existing operating system, we recommend splitting the drive directly on your current OS to create a new partition for BlissOS. This way you won't have to spend time with the partition tools anymore.

On Windows you can use third-party software or internal one like [Disk Management](https://learn.microsoft.com/en-us/windows-server/storage/disk-management/overview-of-disk-management).

!!!danger

    Do not trying to install the OS on an existing partition just so you don't have to split the drive. We do not recommend this practice !

## Flash the images

Download the iso at [https://blissos.org](https://blissos.org) and your preferred media flashing tool. Please refer to the following for which tool you are going to use:

### Using balenaEtcher

Open Etcher, choose the iso in `Flash from file `

![](../../../assets/etcher_01.png){ width="500" }

Plug your USB or SD Card in and choose it at `Select target`

![](../../../assets/etcher_02.png){ width="500" }

Then click `Flash!` to flash the image.

![](../../../assets/etcher_03.png){ width="500" }

Once done, congrats, you now have a bootable BlissOS drive 🥳

### Using Rufus

Open Rufus, plug your USB or SD Card in and select it

![](../../../assets/rufus_01.png){ width="500" }

**SELECT** the ISO image

![](../../../assets/rufus_02.png){ width="500" }

No need to touch any other options, just click **START** <br>
When asked to choose between `Write in ISO Mode` or `Write in DD Mode`, choose `Write in DD Mode`

![](../../../assets/rufus_03.png){ width="500" }

Rufus will ask one last time about removing all data in the USB. If you are prepared then click **OK** to start flashing

![](../../../assets/rufus_04.png){ width="500" }

Once done, congrats, you now have a bootable BlissOS drive 🥳

## Boot into the installer

!!!warning

    BlissOS doesn't support secure boot, if you are on UEFI and your secure boot is on, please disable it to be able to continue the installation !

Reboot your PC, go to the boot menu on your device, choose the flashed BlissOS drive. You'll see the boot loader menu appear:

![](../../../assets/bootmenu.png){ width="500" }

Move down and select `Install BlissOS` and you will enter the installation environment

## Getting Started with the installation environment

The installation environment is a minimal Linux environment that serves [Calamares installer](https://calamares.io/) with a set of necessary tools to debug and diagnose the installation. From here, you can install, update, fix your BlissOS instance with guided instructions.

Here is a screenshot showing details about the installation environment:

![](../../../assets/aaropa.png){ width="500" }

## Install BlissOS

### Select bootloader

As you entered the installation environment, a menu selecting bootloader will soon show up:

![](../../../assets/install_chooseboot.png)

We currently support within the installation process of 2 different bootloaders, you can choose one and continue:

- GRUB (recommended)
- rEFInd

If you want to dualboot with preinstalled GRUB on Linux, select `None` and later after installed, return to your linux distro and install [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober).

!!!info

    If you want to configure bootloader yourself, select `None` and check out [Manual Setup](/installation/manual) section to know how to configure BlissOS into your bootloader manually !


### Enter Calamares

Calamares will appear:

![](../../../assets/install_start.png){ width="500" }

Click `Next`

### Partitioning

You will see the partition screen like this:

![](../../../assets/install_partition.png){ width="500" }

If you have multiple drives, choose the one you want to install BlissOS in at `Select storage device`

There's 3 partitioning options available: `Install alongside`, `Replace a partition`, `Erase disk`, `Manual`.Please refer to the following for selecting the right option for you:

#### Install alongside

The program will automatically allocate a partition from free space and install BlissOS on it.
This option is recommended for most cases.

![](../../../assets/install_alongside.png){ width="500" }

#### Replace a partition

Only choose this option once and only if you already cut a partition for BlissOS. 
Then pick the desired partition in the lower side (and optionally, select your preferred filesystem under the option) like below:

!!!warning

    `ext4` is the default and the recommend filesystem for BlissOS. If you don't know what you are doing or you just want a "just work" installation, keep `ext4` as the chosen filesystem !

![](../../../assets/install_replace.png){ width="500" }

And you're ready to go!

#### Erase disk

If you have an empty disk and want to put BlissOS in, choose this option. The program will automatically format the entire drive, prepare the partitions and install BlissOS on it.

!!!warning

    `ext4` is the default and the recommend filesystem for BlissOS. If you don't know what you are doing or you just want a "just work" installation, keep `ext4` as the chosen filesystem !

![](../../../assets/install_erase.png){ width="500" }

#### Manual partitioning

!!!danger

	This is for people who knows what they are doing! If you're not any familiar with Linux, you should pick the first 2 options. If you're already experienced using Calamares, please note that you're taking responsible for whatever happens with your machine once you choose to process on your own.

![](../../../assets/install_manual.png){ width="500" }

You should be navigated into this advanced partition dashboard like this:

![](../../../assets/install_manual_main.png){ width="500" }

The [requirements](/knowledgebase/hardware-requirement) of BlissOS include minimum 20GB of storage, so you need to `Create` a partition (or `Edit` an existing one) with more than 20GB (any filesystem, but `ext4` is recommended) and mountpoint at `/`.

!!!warning

	A partition with mountpoint at `/` is required for BlissOS to process the installation.

![](../../../assets/install_manual_rootpart.png){ width="500" }

If you want a separated userdata partition just go on with your desired partition with mountpoint at `/data`.

![](../../../assets/install_manual_datapart.png){ width="500" }

!!!danger

	If your machine is running on UEFI, you will need a 100MB or more FAT32 [ESP](https://en.wikipedia.org/wiki/EFI_system_partition) partition too. If it already exists, select it, `Edit`, set mountpoint to `/boot/efi`, and **DO NOT TICK ON FORMATTING**!!! This is crucial because if you do, it will override all bootloader of other OS (Windows, Mac, Linux,...). Instead, tick on `Keep`.

	![](../../../assets/install_manual_espart.png){ width="500" }

The final result should look like this:

![](../../../assets/install_manual_result.png){ width="500" }

!!!warning

    On devices using UEFI, you may see this warning

	![](../../../assets/install_espwarn.png)

	If you see that your ESP still have free space (about 50MB or more), you don't have to worry about this message and can just click `OK` to ignore it.

### Create data image

After you've done previous step and navigating to the next step, a dialog will appear asking whether you want to create a `data.img` file or not. This file will contain all of your user data instead of putting it in a directory. To know which option to choose, head over to [Userdata options](/knowledgebase/userdata-options).

!!!info

	If you have no idea what this is about, just skip by clicking `No`.

![](../../../assets/install_dataimg_msg.png){ width="500" }

This step allows you to customize the size of the data.img disk image. You can adjust the data.img size, or let the installer handle it by keeping `Use maximum` in check.

![](../../../assets/install_dataimg.png){ width="500" }

### Select kernel parameters

!!!info

	Please refer to [BlissOS' kernel parameters cheat sheets](/knowledgebase/kernel-parameters-cheat-sheet) to know what to set. If you have no idea what this is about, just skip it.

You can expand and select (and edit) the kernel argument options. Each of the options indicates the kernel argument (with value) to be passed to the kernel when it is booting.

![](../../../assets/install_cmdline.png){ width="500" }

!!!danger

	**DO NOT, UNDER ANY CIRCUMSTANCES, PICK ALL OPTIONS AT ONCE**. It's already being mentioned on top of the installer screen but we want to reiterate it here. If you pick all options at once it will cause BlissOS unbootable so again **DO NOT, UNDER ANY CIRCUMSTANCES, PICK ALL OPTIONS AT ONCE**.

### Summary

Once you've finished configuring the installation, click `Install` to begin the installation process.

![](../../../assets/install_overview.png){ width="500" }

![](../../../assets/install_process.png){ width="500" }

### Finished

Once the installation is complete, congrats 🥳 ! You now have BlissOS on your PC, click `Done` to finish the installation and restart the device to be able to boot into your new installation of BlissOS!

![](../../../assets/install_done.png){ width="500" }

## Post-installation

![](../../../assets/postinst.png){ width="500" }
/// caption
	attrs: {class: "inline start"}
Screenshot of BlissOS' GRUB
///
Once restarted, you will be able to see BlissOS' GRUB screen (or rEFInd, depending on wait you choose earlier), press Enter to boot into your BlissOS installation.

If you have any issue with the OS, head over to [Configuration](/configuration) to learn more about how to configure the OS to suit your need.
<br><br><br><br><br> <!-- someone pls doing CSS trick so I don't have to do this -->

## Addendum
### Upgrading/Repairing BlissOS using Manual Partitioning

BlissOS supports manually upgrading (or repairing) the current installation by using [Manual Partitioning](#manual-partitioning).<br>
Here's how to do it:

- Boot into the installer and choosing the bootloader that you've chosen earlier.
- Select [Manual Partitioning](#manual-partitioning)
- On the manual partition dashboard, `Edit` the partition that contain BlissOS, set Mount Point to `/` and select `Keep` in Content. If you are on UEFI, do the same for your [ESP](https://en.wikipedia.org/wiki/EFI_system_partition) by setting Mount Point to `/boot/efi` (and the same for `/data` if you set it earlier).

!!!warning

	Double-check to make sure you don't accidentally format your installation.

![](../../../assets/install_manual_upgrade.png){ width="500" }

- If you use `data.img`, you don't have to recreate it in [Create data image](#create-data-image), so just press `No`.
- Set the chosen [Kernel parameters](#select-kernel-parameters) that you've chosen previously.
- Start the installation to upgrade/repair BlissOS.

### Getting logs if the installer failed

