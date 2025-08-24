# Userdata Options

This part will talk about all the available options to store userdata and compare all of them to choose for suitable need !

Before reviewing the options in detail, please consult this table to see which features each one includes:

{{ read_csv('assets/csv/userdata.csv') }}

## Using separated/individual userdata partition

In this case, your data will be stored in a separated partition with mountpoint at `/data`. The partition will be formatted into supported filesystems and mounted using Android's [vold](https://source.android.com/docs/core/storage/config).

Data encryption is supported in this option using [File-based Encryption](https://source.android.com/docs/security/features/encryption/file-based) (although it's still in planning phase, so not available yet!).

!!!warning

	We only support these Linux native filesystems: `ext4`, `btrfs` and `f2fs`. For `xfs`, `jfs` & other Linux native filesystems: we currently don't have a plan to support it yet. For `ntfs`, `exfat` & other non-Linux native filesystems: Android doesn't support them.

## Using data.img file

In this case, your data will be stored in a `data.img` file. The size of `data.img` is resizable & the filesystem for it is hardcoded to be `ext4` & it can be used as the storage options for all kind of BlissOS installation setup (For example: install it on an `ntfs` drive). Another major advantages it provide is the portability of the file as you can just copy `data.img` as a backup.

We've programmed our initrd so that `data.img` can be mounted using [vold](https://source.android.com/docs/core/storage/config). Also, like [userdata partition](/knowledgebase/userdata-options/#using-separatedindividual-userdata-partition), data encryption is supported in this option using [File-based Encryption](https://source.android.com/docs/security/features/encryption/file-based) (although it's still in planning phase, so not available yet!).

!!!info

	If root filesystem isn't Linux native filesystem, `data.img` is automatically created.

## Using `data` directory as userdata

In this case, your data will be stored in `/data` directory placed on your BlissOS installation drive. You can only use this option if root filesystem is Linux native filesystem. This feature was inherited from [Android-x86](https://www.android-x86.org/), what it does it basically bind-mount the `data` directory to `/data` of Android root, and because of that, this option gave us some features that aren't available on any other options:

- Support `Apply from Sdcard` when using [Recovery Mode](/configuration/recovery). Because `/data` is already bind-mounted on initrd, we don't have to do anything extra in Recovery Mode and can just open the directory using the option.
- Supports loading `modules.blocklist` & `modules.options` for blocking kernel modules from loading or setting extra options. On Android init, modules loading triggers before `/data` got mounted, so with `data` directory, it already mounted on initrd, making the config files be able to read by the modules loading process.

With these advantages, `data` directory usually being used internally by us the most for debugging & testing. However, because it is not being managed by [vold](https://source.android.com/docs/core/storage/config), [File-based Encryption](https://source.android.com/docs/security/features/encryption/file-based) is not supported! Also, with the recent update in Android's Storage Manager, it is unknown that this option will remain with us for long.

!!!warning

	We only support these Linux native filesystems: `ext4`, `btrfs` and `f2fs`.

## Using ramdisk as userdata

This option basically stores your data into RAM, and will not save your data after you reboot/power off the device. It does not have any advantages like those above & it's only being used on Live Mode.
