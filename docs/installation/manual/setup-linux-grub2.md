# Setup dual-boot with GNU/Linux using it's own GRUB2

If you install linux on your device, chances are you are using [GRUB2](https://www.gnu.org/software/grub/manual/grub/grub.html) to boot the OS. In this section, we will show you how to setup GRUB2 on Linux to dual-boot BlissOS with it.

!!!Warning
	This section was made using GRUB2 on [Debian 13](https://wiki.debian.org/GRUB2) with UEFI in mind! For every other Linux distribution, find it's document about GRUB2 to learn how to add and apply custom entries for the bootloader !

	[Arch Linux](https://wiki.archlinux.org/title/GRUB) <br>
	[Fedora](https://fedoraproject.org/wiki/GRUB_2) <br>
	[Ubuntu](https://help.ubuntu.com/community/Grub2) <br>
	[OpenSUSE Leap](https://doc.opensuse.org/documentation/leap/reference/html/book-reference/cha-grub2.html) <br>
	[Gentoo](https://wiki.gentoo.org/wiki/GRUB) <br>

## Preparation
- A Linux distribution with GRUB2 installed. 
- BlissOS installation prepared using [The basics](the-basics.md) or [Bootable Installer with no bootloader chosen](../auto/bootable-installer.md#select-bootloader).

## Automatically generate configuration using `grub-android-prober`

You can automatically generate entries for BlissOS using [grub-android-prober](https://github.com/Ananda-Aropa/grub-android-prober). Just need to install the package, regenerate GRUB2 configuration and you're good to go!

For example, this is to install version `0.1.4-5` to Debian:
```sh
wget "https://github.com/Ananda-Aropa/grub-android-prober/releases/download/0.1.4-5/grub-android-prober_0.1.4-5_all.deb"
sudo apt install ./grub-android-prober_0.1.4-5_all.deb
sudo update-grub
```

!!!info
	`grub-android-prober` also supports Arch Linux. Or if you don't use one of these two distributions, check [Quick Install](https://github.com/Ananda-Aropa/grub-android-prober?tab=readme-ov-file#quick-install) to learn how to install it.

Additionally, if you want to add custom kernel parameters, create a file called `cmdline.txt` in the same place where you put BlissOS in. In `cmdline.txt`, add your custom kernel parameters, for example:

```
quiet androidboot.insecure_adb=1 androidboot.enable_console=1
```

If you don't know which kernel parameter to set, please refer to [BlissOS' kernel parameters cheat sheets](../../knowledgebase/kernel-parameters-cheat-sheet.md).

!!!warning
	For those who set up AB-Mode, append `cmdline="androidboot.slot_suffix=_a androidboot.mode=normal"` (to set slot to A and boot mode to normal) to the `/boot/ab.env.cfg` file in the deployment directory (create the file if it's not present), append `androidboot.bootctrl_bootcfg=/boot/ab.env.cfg` to the `cmdline.txt` file.

Once done, regenerate GRUB2 configuration and you're good to go !

```sh
sudo update-grub
# or
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Manually writing configuration

Create and append the following to the `/etc/grub.d/40_blissos` file for custom menu entry:

```sh
menuentry "BlissOS" {
	insmod all_video
	search --set=root --file <src>/kernel
	linux <src>/kernel SRC=<src> <kernel_params>
	initrd <src>/initrd.img
}
```

Replace `<src>` with the path to the BlissOS deployment directory, relative to the root directory of the partition mountpoint (remove it if it's already in the root directory) and `<kernel_params>` with your kernel parameters.

If you don't know which kernel parameter to set, please refer to [BlissOS' kernel parameters cheat sheets](../../knowledgebase/kernel-parameters-cheat-sheet.md).

For example:
```sh
menuentry "BlissOS" {
	insmod all_video
	search --set=root --file /kernel
	linux /kernel SRC=/ syscall_hardening=off VIRT_WIFI=1
	initrd /initrd.img
}
```

If you put BlissOS under `/blissos`
```sh
menuentry "BlissOS" {
	insmod all_video
	search --set=root --file /blissos/kernel
	linux /blissos/kernel SRC=/ syscall_hardening=off VIRT_WIFI=1
	initrd /blissos/initrd.img
}
```

We also provide [Recovery Mode](../../configuration/recovery.md). To add an extra entry for it, copy the entry above but change/add androidboot.mode=recovery
```sh
menuentry "BlissOS Recovery Mode" {
	insmod all_video
	search --set=root --file /kernel
	linux /kernel SRC=/ androidboot.mode=recovery syscall_hardening=off VIRT_WIFI=1
	initrd /initrd.img
}
```

If you set up [AB-mode](../manual/the-basics.md#for-ab-mode-deployment), change `/kernel` to `/kernel_a`, `/initrd.img` to `/initrd_a.img` and append `androidboot.slot_suffix=_a androidboot.mode=normal androidboot.bootctrl_bootcfg=<where_is_generated_config>` to the kernel parameters (the `linux` line).

`<where_is_generated_config>` is the generated `grub.cfg` from the linux distribution you are using. On Debian, this file is in `/boot/grub`

With these info, we can come up with this example for AB-mode setup:
```
menuentry "BlissOS" {
	insmod all_video
	search --set=root --file /kernel_a
	linux /kernel_a SRC=/ androidboot.slot_suffix=_a androidboot.mode=normal androidboot.bootctrl_bootcfg=/boot/grub/grub.cfg syscall_hardening=off VIRT_WIFI=1
	initrd /initrd_a.img
}
menuentry "BlissOS Recovery Mode" {
	insmod all_video
	search --set=root --file /kernel_a
	linux /kernel_a SRC=/ androidboot.slot_suffix=_a androidboot.mode=recovery androidboot.bootctrl_bootcfg=/boot/grub/grub.cfg syscall_hardening=off VIRT_WIFI=1
	initrd /initrd_a.img
}
```

Save the file once done, reload the configuration using
```
sudo update-grub
```


## Reboot and test

Once you got GRUB2 setup, reboot the device. You will be able to see BlissOS entry in the GRUB2 screen.

If everything works correctly then congrats 🥳.

!!!danger
	If you are using [AB-mode](../manual/the-basics.md#for-ab-mode-deployment), please be cautious that sometimes when updating packages will reload GRUB2 configuration, which will reset the current slot of your BlissOS to slot A. If you are on slot B, sudden slot changing might accidentally downgrade the OS and can cause issues. We advise you to look at the `grub.cfg` file to double check whenever you're done with a Linux update!

