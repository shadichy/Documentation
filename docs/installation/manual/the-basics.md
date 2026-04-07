# The basics

Understanding the basics of what to do & what to prepare before starting manual setup.

## Prerequisites

You'll need these following:

- A 20+ GB storage partition (as stated in [Hardware Requirement](../../knowledgebase/hardware-requirement.md))
- A working Linux environment 

!!! Info
	If you only have Windows, ensure that at least you have [dd](https://www.msys2.org/), [erofs-utils](https://github.com/sekaiacg/erofs-utils) (or [squashfs-tools](https://infraroot.at/pub/squashfs/windows/), depends on the system.efs/sfs inside the iso, [7zip-zstd](https://github.com/mcmilk/7-Zip-zstd) can also be used for `system.sfs`) with a Linux filesystem driver and toolchain ([WinBtrfs](https://github.com/maharmstone/btrfs), [Ext4Fsd](https://github.com/bobranten/Ext4Fsd), 3rd-party partition manager, etc) or just get WSL2 installed. These will be used to extract/create required files for BlissOS.

- A bootloader/boot manager that supports booting sparse images (both kernel & initrd)

!!! Warning
	A sparse image is a file that has been created by using the `dd` command with the `conv=notrunc` option. A bootable sparse image is a kernel or initrd image created using the same technique.

	Most bootloaders prohibit booting sparse images, or unknown capability.
	As for now, known sparse-supported bootloaders are: [GRUB](https://www.gnu.org/software/grub/) (works with non-CoW filesystems), [RefindPlus](https://github.com/RefindPlusRepo/RefindPlus) (this one is not recommended due to stability issues).
	Unsupported (tested) bootloaders: [rEFInd](https://www.rodsbooks.com/refind/).
	Unplanned/Rejected: [limine](https://github.com/limine-bootloader/limine)

- A BlissOS .iso image (duh)

## Setting up place to put BlissOS in

If you are using Linux with a supported filesystem and ***unencrypted***, you can create a directory at `/` and putting BlissOS on it. For example, you can create `/blissos` like this

![](../../assets/images/manual/linuxdir.png){ width="500" }

Otherwise, you can create a separate partition with supported filesystem.

!!!info

	BlissOS only natively supports `ext4`, `f2fs` and `btrfs` filesystems. If you want to deploy on other filesystems (such as `ntfs` or `exfat`), you may have to take some extra steps, and a `data.img` is required.

!!!info

	If you want to deploy BlissOS nested in a folder (like putting it under a Linux `/` drive that we mentioned earlier), note the relative path to the folder for the `SRC=` kernel parameter (For example `SRC=/blissos`).


## Copy BlissOS files

Mount the BlissOS .iso image. You will need to pull these files out:

- `kernel`
- `initrd.img`
- `system.efs` (or `system.sfs` on older version)
- `ramdisk-recovery.img`

<!-- 
!!!info

	For BlissOS 19 or later, we will also need to copy a `vendor.img` file.

Depend on the purpose, you may want to deploy BlissOS A-only or AB-mode (OTA-ready). -->

Once you got the files, follow one of these two method to deploy the images:

### For A-only deployment

In this mode, you will get BlissOS running but you **cannot** be able to update using internal updater or from .iso image.

Just need to copy the files from BlissOS .iso to where you want to put BlissOS in, and navigate to the next step.

### For AB-mode deployment

In this mode, your BlissOS can be updated using internal updater (OTA-ready) or from .iso image.

!!!info "Did you know ?"
	You can setup proper AB-mode into an empty partition using our [bootable installer](../auto/bootable-installer.md) ! When choosing Bootloaders, just pick [None](../auto/bootable-installer.md#select-bootloader).

The rootfs must contain 2 replicas of each file with exact same sizes:
- `kernel` -> `kernel_a` and `kernel_b`
- `initrd.img` -> `initrd_a.img` and `initrd_b.img`
- `system.img` (inside `system.efs` or `system.sfs`) -> `system_a.img` and `system_b.img`
- `ramdisk-recovery.img` -> `recovery_a.img` and `recovery_b.img`

This is due to OTA update method requires 2 slots (AB), one for booting and one for flashing next update's OTA image. In case the update fails, it will fallback to the other (current) slot.

!!!info
	As of "why should the replicas have the same sizes?", it's because OTA updates by writing a payload to preexisting block devices, not writing files. The "replica" block devices are not allowed to have different sizes than the original. But no update has the same size as the other, so we must define a "ceiling" value for the size, so newer updates have space to grow.

There are 2 ways:
1. Rename the files with adding suffix `_a`, then create the replicas with the suffix `_b`, with the size of the ceiling value, then calculate the difference in size between the 2 files and grow the original files to the ceiling.
2. Create empty containers for both `_a` and `_b` files, then clone the original files into the `_a` containers.

This is the 2nd way.
Do as following:
```sh
# create empty containers for files
# kernels and initrds would require <= 20MB, setting 20MB as ceiling should be enough
# system.img is about ~4.8GB, ceil=5GB
# recovery.img is < 40MB, ceil=40MB
dd if=/dev/zero of=kernel_a bs=1M count=0 seek=20
dd if=/dev/zero of=kernel_b bs=1M count=0 seek=20
dd if=/dev/zero of=initrd_a.img bs=1M count=0 seek=20
dd if=/dev/zero of=initrd_b.img bs=1M count=0 seek=20
dd if=/dev/zero of=system_a.img bs=1G count=0 seek=5
dd if=/dev/zero of=system_b.img bs=1G count=0 seek=5
dd if=/dev/zero of=recovery_a.img bs=1M count=0 seek=40
dd if=/dev/zero of=recovery_b.img bs=1M count=0 seek=40

# mount system.efs/system.sfs
mkdir out
sudo mount system.?fs out

# clone files
dd if=kernel of=kernel_a conv=notrunc
dd if=initrd.img of=initrd_a.img conv=notrunc
dd if=out/system.img of=system_a.img conv=notrunc # retry with `sudo` if needed
dd if=ramdisk-recovery.img of=recovery_a.img conv=notrunc

# unmount system.efs/system.sfs
sudo umount out && rm -rf out

# delete original files
rm kernel initrd.img system.efs/system.sfs ramdisk-recovery.img
```

#### Misc image

A 10MB `misc.img` is required for the AB-mode setup. Create it with:

```sh
dd if=/dev/zero of=misc.img bs=1M count=10
```

All the commands must be run in the same directory as the BlissOS deployment. <br>
Once done, your files should look like this

![](../../assets/images/manual/imgsize.png)

## Data image

If you want to create a data.img, or you want to use other filesystems than `ext4` or `btrfs`, you can do it by:

```sh
dd if=/dev/zero of=data.img bs=1M count=<size_in_MB>
mkfs.<your_chosen_filesystem> data.img
```

For example
```sh
dd if=/dev/zero of=data.img bs=1M count=32000
mkfs.ext4 data.img
```
!!!warning

    `ext4` is the default and the recommend filesystem for BlissOS. If you just want a "just work" installation, keep `ext4` as the chosen filesystem !

All the commands must be run in the same directory as the BlissOS deployment.

## Making a custom fstab

You will need to create a `fstab.android` file in the same place you put BlissOS in. To know how to create this file, check out [Pseudo filesystem table](../../configuration/pseudo-filesystem-table.md)

## Bootloader setup

!!!danger
	If you setup BlissOS with AB-mode, please install a bootloader that allow setting custom entry using a file. If it doesn't expose a file, the updater **will not work**! An example of this is `EFIStub`.

We will assume that you've installed a bootloader. This part will just talk about how to setup a custom entry for BlissOS.

First, custom entry should tell you to specify which partition to look at (for example rEFInd has [volume](https://www.rodsbooks.com/refind/configfile.html#stanzas) that check for partitions' GUID or label, GRUB has [set root](https://www.gnu.org/software/grub/manual/grub/grub.html#Naming-convention)). Point it to the partition that you put BlissOS in earlier.

Usually bootloaders will have these 3 main parts for a custom entry:

```
linux <where_is_your_kernel>
initrd <where_is_your_initrd_img>
options <which_cmdline_to_set>
```

or if it doesn't have `options`:

```
linux <where_is_your_kernel> <which_cmdline_to_set>
initrd <where_is_your_initrd_img>
```

`<where_is_your_kernel>` and `where_is_your_initrd_img` are the location of your BlissOS' `kernel` and `initrd.img` , `<which_cmdline_to_set>` is the custom kernel parameter that you want to set. 

If you don't know which kernel parameter to set, please refer to [BlissOS' kernel parameters cheat sheets](../../knowledgebase/kernel-parameters-cheat-sheet.md).

!!!danger

	For all types of deployment, `SRC=` must be set, this is the location that initrd will look for installation of BlissOS !

	If your are on AB-Mode, these kernel parameters **must be set**:

	- `androidboot.slot_suffix=_a`: For setting slot suffix (`_a` or `_b`).
	- `androidboot.mode=normal`: For setting boot mode (`normal` or `recovery`).
	- `androidboot.bootctrl_bootcfg=/path/to/boot_config_file`: For setting boot configuration file (Refer to [Boot configuration file path](../../knowledgebase/kernel-parameters-cheat-sheet.md#booting-startup)).

With these information, your custom entry should look like this:

```
linux /kernel
initrd /initrd.img
options SRC=/ intel_idle.max_cstate=2 syscall_hardening=off
```

Or, if you put BlissOS in a directory

```
linux /blissos/kernel
initrd /blissos/initrd.img
options SRC=/blissos intel_idle.max_cstate=2 syscall_hardening=off
```

If you are using AB-mode, it should be like this

```
linux /kernel_a
initrd /initrd_a.img
options SRC=/ androidboot.slot_suffix=_a androidboot.mode=normal androidboot.bootctrl_bootcfg=/boot/my_bt/custom.conf intel_idle.max_cstate=2 syscall_hardening=off
```

We also provide [Recovery Mode](../../configuration/recovery.md). To add an extra entry for it, copy the entry above but change/add `androidboot.mode=recovery`

```
linux /kernel
initrd /initrd.img
options SRC=/ androidboot.mode=recovery intel_idle.max_cstate=2 syscall_hardening=off
```

Once done, reboot and check to see if your custom entry is there.

If you still have trouble setting up the entry, we provided some tutorial for specific bootloaders that you can use as an example. Head over to the Table of contents to see it.


If everything works correctly then congrats 🥳. You've succeeded in manually installing BlissOS.
