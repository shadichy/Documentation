# Setup dual-boot with GNU/Linux using it's own Grub2

page for set up grub2 that Linux gave

## Preparation

## Writing a custom configuration file

## Reload grub.cfg

## Reboot and test
<!-- 

If you want to dualboot with preinstalled GRUB on Linux, select `None` and later after installed, return to your linux distro and install [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober).


#### Automatically probed using grub-android-prober

You actually don't need to configure anything outside of appending kernel parameters to the `cmdline.txt` command line. On your linux side, install [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober) and regenerate GRUB configuration and there you go.

For those who set up AB-Mode, append `cmdline="androidboot.slot_suffix=_a androidboot.mode=normal"` (to set slot to A and boot mode to normal) to the `boot/ab.env.cfg` file in the deployment directory (create the file if it's not present), append `androidboot.bootctrl_bootcfg=/boot/ab.env.cfg` to the `cmdline.txt` file.

#### Manual configuration

Create and append the following to the `/etc/grub.d/40_blissos` file for custom menu entry:

```sh
menuentry "BlissOS" {
	insmod all_video
	search --set=root --file <src>/kernel
	linux <src>/kernel SRC=<src> <kernel params>
	initrd <src>/initrd.img
}
```

Replace `<src>` with the path to the BlissOS deployment directory, relative to the root directory of the partition mountpoint (remove it if it's already in the root directory) and `<kernel params>` with your kernel parameters.

If you set up AB-Mode, change `/kernel` to `/kernel_a`, `/initrd.img` to `/initrd_a.img` and append `androidboot.slot_suffix=_a androidboot.mode=normal` to the kernel parameters (the `linux` line).

Save the file and regenerate GRUB configuration. -->
