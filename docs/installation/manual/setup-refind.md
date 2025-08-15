# Setup booting on rEFInd

page for setting up with rEFInd

## Preparation

## Writing a custom configuration file

## Reboot and test

<!-- 

Create `/boot/efi/EFI/refind/blissos.conf` for custom menu entry.

Append to the `/boot/efi/EFI/refind/blissos.conf` file:

```sh

menuentry "BlissOS" {
  volume <your partition identifier>
  loader <src>/kernel
  initrd <src>/initrd.img
  options "androidboot.mode=normal SRC=<src> <kernel params>"
}
```

Replace `<src>` with the path to the BlissOS deployment directory, relative to the root directory of the partition mountpoint (remove it if it's already in the root directory), replace `<kernel params>` with your kernel parameters, and `<your partition identifier>` with the partition UUID (partuuid) or label (partlabel) of the partition you deploy BlissOS on (look up `/dev/disk/by-partuuid` or `/dev/disk/by-partlabel`).

If you set up AB-Mode, change `/kernel` to `/kernel_a`, `/initrd.img` to `/initrd_a.img` and append `androidboot.slot_suffix=_a androidboot.mode=normal` to the kernel parameters (the `options` line). -->
