# Setup booting on rEFInd

If you are using [rEFInd Boot Manager](https://www.rodsbooks.com/refind/), this section will show you how to setup and boot BlissOS using it.

!!!Warning
    We will assume that you've installed [rEFInd](https://www.rodsbooks.com/refind/) on your device. If not, check [rEFInd Instruction](https://www.rodsbooks.com/refind/installing.html#linux) to see how to install it. Or if you are using Linux, check your distributions documents (for example [Arch Linux](https://wiki.archlinux.org/title/REFInd)) instead.

## Preparation
- A device with rEFInd installed.
- BlissOS installation prepared using [The basics](the-basics.md) or [Bootable Installer with no bootloader chosen](../auto/bootable-installer.md#select-bootloader).

## Writing a custom configuration file

Mount and open up your [EFI System Partition](https://en.wikipedia.org/wiki/EFI_system_partition), you will see rEFInd under `/EFI/refind`. In this directory, create a text file called `blissos.conf` and append the following:

```sh
menuentry "BlissOS" {
  volume <your partition identifier>
  loader <src>/kernel
  initrd <src>/initrd.img
  options "androidboot.mode=normal SRC=<src> <kernel_params>"
}
```

(This config is based on [Creating Manual Boot Stanzas](https://www.rodsbooks.com/refind/configfile.html#stanzas) page.)

Replace `<src>` with the path to the BlissOS deployment directory, relative to the root directory of the partition mountpoint, replace `<kernel_params>` with your kernel parameters, and `<your_partition_identifier>` with the partition UUID (`partuuid`) or label (`partlabel`) of the partition you deploy BlissOS on.

If you don't know which kernel parameter to set, please refer to [BlissOS' kernel parameters cheat sheets](../../knowledgebase/kernel-parameters-cheat-sheet.md).

!!!info
    If you don't know your `partuuid` or `partlabel`, use a 3rd-party partition manager or look up `/dev/disk/by-partuuid` or `/dev/disk/by-partlabel` or use `blkid` if you are using Linux.

With this info, we come up with this example:

```sh
menuentry "BlissOS" {
  volume BlissOS
  loader /kernel
  initrd /initrd.img
  options "SRC=/ VIRT_WIFI=1 androidboot.insecure_adb=1"
}
```

In this example, we set `partlabel` as `BlissOS`. Here is another example which we'll use `partuuid` and put BlissOS under `/blissos`:
```sh
menuentry "BlissOS" {
  volume ce3908c4-2dc3-4ef9-b8e4-e3cfb12525c7
  loader /blissos/kernel
  initrd /blissos/initrd.img
  options "SRC=/blissos VIRT_WIFI=1 androidboot.insecure_adb=1"
}
```

Additionally, we can [create submenu entries](https://www.rodsbooks.com/refind/configfile.html#submenu) for options such as Recovery Mode like this:
```sh
menuentry "BlissOS" {
  volume ce3908c4-2dc3-4ef9-b8e4-e3cfb12525c7
  loader /blissos/kernel
  initrd /blissos/initrd.img
  options "SRC=/blissos VIRT_WIFI=1 androidboot.insecure_adb=1"
  submenuentry "Boot with Recovery Mode" {
      options "androidboot.mode=recovery SRC=/blissos VIRT_WIFI=1 androidboot.insecure_adb=1"
  }
}
```

If you set up [AB-mode](../manual/the-basics.md#for-ab-mode-deployment), change `/kernel` to `/kernel_a`, `/initrd.img` to `/initrd_a.img` and append `androidboot.slot_suffix=_a androidboot.mode=normal androidboot.bootctrl_bootcfg=/boot/efi/EFI/rEFInd/blissos.conf` to the kernel parameters (the `options` line).

```sh
menuentry "BlissOS" {
  volume ce3908c4-2dc3-4ef9-b8e4-e3cfb12525c7
  loader /blissos/kernel_a
  initrd /blissos/initrd_a.img
  options "androidboot.slot_suffix=_a androidboot.mode=normal androidboot.bootctrl_bootcfg=/boot/efi/EFI/rEFInd/blissos.conf SRC=/blissos VIRT_WIFI=1 androidboot.insecure_adb=1"
  submenuentry "Boot with Recovery Mode" {
      options "androidboot.slot_suffix=_a androidboot.bootctrl_bootcfg=/boot/efi/EFI/rEFInd/blissos.conf androidboot.mode=recovery SRC=/blissos VIRT_WIFI=1 androidboot.insecure_adb=1"
  }
}
```

Once done, add this line at the end of `refind.conf` file so rEFInd can be able to read the configs:

```
include blissos.conf
```

## Reboot and test

Once you got rEFInd setup, reboot the device. You will be able to see BlissOS entry on rEFInd & submenus can be seen using F2 or Insert key.

If everything works correctly then congrats 🥳.
