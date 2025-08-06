# The basics

Understanding the basics of what to do & what to prepare before starting manual setup

## Prerequisites

You'll need these following:
- A 16+Gb storage partition (of course)
- A working Linux environment (if you only have Windows, ensure that at least you have `dd`, `erofs` tools and `squashfs` tools with a Linux filesystem driver and toolchain (WinBtrfs, Ext4Fsd,...), or WSL installed)
- A BlissOS ISO image

## Setting up place to put BlissOS in

First, format the partition and mount it.

!!!info

	BlissOS only natively supports `extfs` and `btrfs` filesystems. If you want to deploy on other filesystems (such as `ntfs` or `exfat`), you may have to take some extra steps, and a `data.img` is required.

## Copy BlissOS files

Mount the BlissOS ISO image.
The files we need to copy are:
- `kernel`
- `initrd.img`
- `system.efs` (`system.sfs`)
- `ramdisk-recovery.img`

!!!info

	For BlissOS 19 or later, we will also need to copy a `vendor.img` file.

Depend on the purpose, you may want to deploy BlissOS A-only or AB-mode (OTA-ready).

### For A-only deployment

In this mode, you will get BlissOS running but you **cannot** update using internal updater or from ISO image.

Just need to copy the files from the BlissOS ISO mountpoint to the partition mountpoint, and navigate to the next step.

### For AB-mode deployment

In this mode, your BlissOS can be updated using internal updater (OTA-ready) or from ISO image.

First, copy the files from the BlissOS ISO mountpoint to the partition mountpoint, rename `ramdisk-recovery.img` to `recovery.img`.

Then, unpack the `system.efs` with `erofs` tool (if the system image name is `system.sfs`, unpack it with `unsquashfs`) to get the `system.img`. Move it to the root of the partition mountpoint if it's not there. You can later delete the `system.efs` (`system.sfs`) file.

Next, rename the files by appending `_a` as a postfix to the file names and before the file extension (`kernel` -> `kernel_a`, `initrd.img` -> `initrd_a.img`,...). This will be the slot A of your BlissOS.

!!!info

	The size of files in slot A must be equal to the size of files in slot B. But with each update, the payload size may differs. To solve this, we can use `dd` to append empty data to slot A to scale it up to the same size as slot B.

Following that, define the upper size for each of files in slot A, and create slot B files with the defined upper sizes using `dd` in the same directory as slot A. For example:

```sh
# Create system_b.img, upper size is 5GB
dd if=/dev/zero of=system_b.img bs=1G count=5
# ... applies to other files
```

And then scale the slot A files to the upper size using `dd` with `conv=notrunc`. For example:

```sh
# Get the size of system_a.img in MB
size=$(du -B 1M system_a.img | cut -f1)
# > 4679
# Since 5GB is 5120MB, we subtract 5120MB from the size of system_a.img (4679MB) and get 139MB.
# Append 139MB empty data to system_a.img
dd if=/dev/zero of=system_a.img bs=1M count=$((5120 - size)) conv=notrunc
# ... applies to other files
```

## Data image

If you want to create a data.img, or you want to use other filesystems than extfs or btrfs, you can do it by:

```sh
dd if=/dev/zero of=data.img bs=1M count=<size in MB>
mkfs.ext4 data.img
```

All the commands must be run in the same directory as the BlissOS deployment.

## Misc image

A 10MB `misc.img` is required if you have setup AB-mode. Create it by:

```sh
dd if=/dev/zero of=misc.img bs=1M count=10
```

All the commands must be run in the same directory as the BlissOS deployment.

## Bootloader setup
