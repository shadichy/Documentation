# Porting Linux Drivers

When it seems that there might be a missing driver or hardware support for your specific device, the best way to add support for that is to search online for a Linux kernel module for your target hardware. 

Once you've tracked down or created a Linux driver for your device and want to add it in externally, you may want to add it into `external/kernel-drivers` (or `external/kernel-drviers-zenith` for BlissOS Zenith) and then create an Android.mk file. 

```text
#
# Copyright (C) 2018 The Android-x86 Open Source Project
#
# Licensed under the GNU General Public License Version 2 or later.
# You may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.gnu.org/licenses/gpl.html
#

LOCAL_PATH := $(my-dir)
LOCAL_MODULE := $(notdir $(LOCAL_PATH))
EXTRA_KERNEL_MODULE_PATH_$(LOCAL_MODULE) := $(LOCAL_PATH)
```

This Android.mk will put your driver on a list so that [kernel.mk](https://github.com/BlissOS/device_generic_common/blob/typhoon-x86/build/tasks/kernel.mk) can take that list and then build the given extra drivers. 

Sometimes it is not just as easy as putting a small Android.mk file into the source and it can immediately be able to build. What you might want to pay attention might be the Makefile for example, or have to edit some extra C files (mostly related to wifi). It might take time to edit and if you don't know what to do, you can check out our kernel-drivers source as a reference:

https://github.com/android-generic/external_kernel-drivers

Once you got the driver properly built and it works on BlissOS, you can make a [Pull request](https://github.com/android-generic/external_kernel-drivers/pulls) so we can check and merge it in.
