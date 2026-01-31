# BlissOS' kernel parameters cheat sheets

As BlissOS is using Linux kernel, it can inherit all of the [kernel's command-line parameters](https://docs.kernel.org/admin-guide/kernel-parameters.html) available plus some extra parameters to set variables for some of BlissOS init scripts. This part will list all of the custom parameters that BlissOS is using with some extra one from the Linux kernel.

If you want to know how to set these, check out [Setup kernel's command-line parameters](../configuration/kernel-parameters.md).

## Booting & Startup

!!!danger
    These parameters are very important. `SRC` is the **must-have** parameters on your installation of BlissOS. the rest of `androidboot.*` options are **must-have** if you are using [AB-mode](../installation/manual/the-basics.md#for-ab-mode-deployment).

{{ read_csv('../assets/csv/cmdline/booting.csv') }}

## Logging & Debugging

!!!warning
    These parameters should be used for debugging purposes only !

{{ read_csv('../assets/csv/cmdline/debug.csv') }}

## Media codecs

!!!warning
    By default, we already setup media codecs options suitable for each hardware for BlissOS so you don't have to do anything extra. This is mostly for debugging purposes !

{{ read_csv('../assets/csv/cmdline/codecs.csv') }}

## Disks & partitions

### NTFS options

!!!warning
    These options are being used to switch from the usual [NTFS-3G](https://github.com/tuxera/ntfs-3g) driver to the new [NTFS3](https://docs.kernel.org/filesystems/ntfs3.html) driver which provide better NTFS handling & performance. Each represents different level of NTFS support. You can only pick one.

{{ read_csv('../assets/csv/cmdline/ntfs3.csv') }}

### Miscellaneous

{{ read_csv('../assets/csv/cmdline/disk_misc.csv') }}

## Networking

{{ read_csv('../assets/csv/cmdline/network.csv') }}

## Sensors

{{ read_csv('../assets/csv/cmdline/sensors.csv') }}

## Power
### Battery

{{ read_csv('../assets/csv/cmdline/battery.csv') }}

### Miscellaneous

{{ read_csv('../assets/csv/cmdline/power_misc.csv') }}

## Audio

{{ read_csv('../assets/csv/cmdline/audio.csv') }}

## Bluetooth

{{ read_csv('../assets/csv/cmdline/bluetooth.csv') }}

## Graphics

!!!warning
    By default, we already setup graphics options suitable for each hardware for BlissOS so you don't have to do anything extra. This is mostly for debugging purposes !

{{ read_csv('../assets/csv/cmdline/graphics.csv') }}

### Celadon-specific

!!!info
    These options are only available if HWC is either `drm_celadon` or `drm_minigbm_celadon`

{{ read_csv('../assets/csv/cmdline/graphics_celadon.csv') }}

## Recovery

{{ read_csv('../assets/csv/cmdline/recovery.csv') }}

## Camera

{{ read_csv('../assets/csv/cmdline/camera.csv') }}

## Miscellaneous

{{ read_csv('../assets/csv/cmdline/misc.csv') }}
