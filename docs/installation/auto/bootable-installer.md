# Bootable Installer

The bootable installer is based on [Android-x86's newinstaller](https://git.osdn.net/view?p=android-x86/bootable-newinstaller.git;a=summary). While the procedure is still almost the same, there are significant changes that make this installer different from Android-x86 one. This section will show you how to make a flashable installation media and then boot to this installer to install BlissOS.

## Preparation

- A software to flash images to removable media

For removable media such as USB or SD Card, we recommend [balenaEtcher](https://etcher.balena.io/). Etcher is being used internally by us to flash and test the installer on our own hardware. There are other software you can choose, however we don't guarantee that it will work.

!!!info

    BlissOS images can be booted on a CD too ! Although we only test this on virtual machines so far 🤔

- Free space on your hard drive

Check out [hardware requirements](../../../knowledgebase/hardware-requirement) to know how much space you need. After knowing how much space you want to set, if you want to dual-boot with a existing operating system, we recommend splitting the drive directly on your current OS to create a new partition for BlissOS. This way you won't have to spend time with the partition tools anymore.

On Windows you can use third-party software or internal one like [Disk Management](https://learn.microsoft.com/en-us/windows-server/storage/disk-management/overview-of-disk-management).

!!!danger

    Do not trying to install the OS on an existing partition just so you don't have to split the drive. We do not recommend this practice !

## Flash the images

Download the iso at [https://blissos.org](https://blissos.org). Then open Etcher and choose the iso in `Flash from file `

![](../../../assets/etcher_01.png)

Plug your USB or SD Card in and choose it at `Select target` 

![](../../../assets/etcher_02.png)

Then click `Flash!` to flash the image.

![](../../../assets/etcher_03.png)

Once done, you now have an bootable BlissOS drive

## Boot into the installer

!!!warning

    BlissOS doesn't support secure boot, if you are on UEFI and your secure boot is on, please disable it to be able to continue the installation !

