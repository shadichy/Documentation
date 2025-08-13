# Pseudo filesystem table

Pseudo filesystem table, or pseudo fstab, is a virtual fstab file that acts as a mapper table to map necessary files and partitions to `/dev/block/by-name` by identifier.

The file is named `fstab.android` and located in root filesystem (where you installed/deployed BlissOS).

## Syntax

The pseudo fstab file itself is an actual Linux/Android fstab file, with some additional modifications in syntax for extra support for file mapping and directory binding.

The file is a text file represents a table, with the following columns:

- `<src>`: Source file/directory/partition (must be absolute path) \*
- `<mnt_point>`: Target or mount point \*
- `<type>`: Filesystem type
- `<mnt_flags and options>`: Mount flags and options (it's like you're using `mount -o`)
- `<fs_mgr_flags>`: Android filesystem manager flags (vold flags)

> \* For file mapping and directory binding, only `<src>` and `<mnt_point>` are required. Additional fields are required **only** if the file being mapped is a R/W mountable disk image file (`.img`) or a partition. For partition mounting, all fields are required.

!!!info

	Linking a partition to a specific path also count as a file mapping.

### Special mount targets

We've defined some special mount targets for file mapping and directory binding, representing standard Android block devices:

- `system_a`: Will be mapped to `/dev/block/by-name/system_a`.
- `system_b`: Will be mapped to `/dev/block/by-name/system_b`.
- `vendor_a`: Will be mapped to `/dev/block/by-name/vendor_a`.
- `vendor_b`: Will be mapped to `/dev/block/by-name/vendor_b`.
- `kernel_a`: Will be mapped to `/dev/block/by-name/kernel_a`.
- `kernel_b`: Will be mapped to `/dev/block/by-name/kernel_b`.
- `recovery_a`: Will be mapped to `/dev/block/by-name/recovery_a`.
- `recovery_b`: Will be mapped to `/dev/block/by-name/recovery_b`.
- `initrd_a`: Will be mapped to `/dev/block/by-name/initrd_a`.
- `initrd_b`: Will be mapped to `/dev/block/by-name/initrd_b`.
- `misc`: Will be mapped to `/dev/block/by-name/misc`.
- `userdata`: Will be mapped to `/dev/block/by-name/userdata` or bound directly to `/data` if data is directory (You can replace this with direct `/data` if you don't want vold to handle your data.img or userdata partition, in that case additional column fields are required).
- `bootloader`: Will be bound to `/boot`.

### Variables

We provide variables to simplify the syntax and automate the mapping/binding tasks:

- `$SLOT`: Slot suffix (`_a` or `_b`), parsed from kernel parameter `androidboot.slot_suffix`. Useful if you're using A/B mode.
- `$FS`: Path to directory you installed BlissOS to (filesystem absolute path, or relative to the root directory of the partition mountpoint), parsed from kernel parameter `SRC`. Useful if you nested BlissOS installation in a directory.