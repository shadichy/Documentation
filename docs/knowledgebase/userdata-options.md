# Userdata Options

This part will talk about all the available options to store userdata and compare all of them to choose for suitable need !

## Using separated/individual data partition

In this case, your data will be stored in a separated partition with mountpoint at `/data`. The partition can be customized with whatever you want.

Data encryption is supported in this option.

!!!info

	We only support these Linux native filesystems: `ext2`/`ext3`/`ext4`, `btrfs` and `f2fs`. For `xfs`, `jfs` & other Linux native filesystems: we currently don't have a plan to support it yet. For `ntfs`, `exfat` & other non-Linux native filesystems: Android doesn't support them.

## Using data.img file

In this case, your data will be stored in a `data.img` file. The file can be customized only in size only. The filesystem for the `data.img` is hardcoded to be `ext4`. You can still reformat it to the filesystem you want (must be one of `ext2`/`ext3`/`ext4`, `btrfs` or `f2fs`). Beware for data loss.

Data encryption is supported in this option.

!!!info

	If root filesystem isn't Linux native filesystem, `data.img` is automatically created.

## Using data directory

In this case, your data will be stored in `/data` directory under root filesystem. You can only use this option if root filesystem is Linux native filesystem.

Data encryption is **not** supported in this option.