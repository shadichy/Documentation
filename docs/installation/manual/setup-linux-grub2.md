# Setup dual-boot with GNU/Linux using it's own GRUB2

In this section, we will show you how to setup GRUB2 on Linux to dual-boot BlissOS with it. 

!!!Warning
	This section was made using GRUB2 on [Debian 13](https://www.debian.org/News/2025/20250809) with UEFI in mind! For every other linux distribution, find it's document about GRUB2 to learn how to add and apply custom entries for the bootloader !

## Preparation
- A Linux distribution with GRUB2 installed. 
- BlissOS installation prepared using [The basics](/installation/manual/the-basics) or [Bootable Installer with no bootloader chosen](/installation/auto/bootable-installer/#select-bootloader).

## Automatically generate configuration using `grub-android-prober`

You can automatically generate entries for BlissOS using [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober). Just need to install the package, regenerate GRUB2 configuration and you're good to go!

For example:
```sh
wget "https://github.com/Ananda-Aropa/grub-android-prober/releases/download/0.1.4-5/grub-android-prober_0.1.4-5_all.deb"
sudo apt install ./grub-android-prober_0.1.4-5_all.deb
sudo update-grub
```

Additionally, if you want to add custom kernel parameters, create a file called `cmdline.txt` 

!!!warning
	For those who set up AB-Mode, append `cmdline="androidboot.slot_suffix=_a androidboot.mode=normal"` (to set slot to A and boot mode to normal) to the `/boot/ab.env.cfg` file in the deployment directory (create the file if it's not present), append `androidboot.bootctrl_bootcfg=/boot/ab.env.cfg` to the `cmdline.txt` file.


## Writing a custom configuration file

## Reload grub.cfg

## Reboot and test
<!-- 

If you want to dualboot with preinstalled GRUB on Linux, select `None` and later after installed, return to your linux distro and install [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober).


#### Automatically probed using grub-android-prober


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
