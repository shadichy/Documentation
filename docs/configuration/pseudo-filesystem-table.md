# Pseudo filesystem table

Pseudo filesystem table, or pseudo fstab, is a virtual fstab file that acts as a mapper table to map necessary files and partitions to `/dev/block/by-name` by identifier.

The file is named `fstab.android` and located in root filesystem (where you installed/deployed BlissOS), automatically created by BlissOS on installation or initial boot.

This guide is for editing/modifying the pseudo fstab file.

## Syntax

The pseudo fstab file itself is an actual Linux/Android fstab file, with some additional modifications in syntax for extra support for file mapping and directory binding.

The file is a text file represents a table, with the following columns:

| Column | Description | Caution | Required for file mapping and directory binding |
| --- | --- | --- | --- |
| `<src>` | Source file/directory/partition | Must be absolute path | ✅ Yes |
| `<mnt_point>` | Target or mount point | Must be absolute path | ✅ Yes |
| `<type>` | Filesystem type | Linux supported filesystems only | 🚫 Case-dependent |
| `<mnt_flags and options>` | Mount flags and options | Similar to using `mount -o`| 🚫 Case-dependent |
| `<fs_mgr_flags>` | Android filesystem manager flags | vold-specific flags | 🚫 Case-dependent |

> "🚫 Case-dependent" fields are required **only** if the file being mapped is a R/W mountable disk image file (`.img`) or a partition. For partition mounting, all fields are required.

!!!info

	Linking a partition to a specific path also count as a file mapping.

### Special mount targets

We've defined some special mount targets for file mapping and directory binding, representing standard Android block devices:

{{ read_csv('assets/csv/fstab_mapper.csv') }}

> You can replace `userdata` with `/data` directly if you don't want vold to handle your data.img or userdata partition, in that situation, all column fields are required.

### Variables

We provide variables to simplify the syntax and automate the mapping/binding tasks. Variables start with an `$` prefix. Please refer to the following table for variable information:

{{ read_csv('assets/csv/fstab_vars.csv') }}