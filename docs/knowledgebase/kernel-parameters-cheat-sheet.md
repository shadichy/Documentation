# BlissOS' kernel parameters cheat sheets

This part will list all of the kernel command-line parameters that BlissOS is using with some extra parameters from linux kernel. If you want to know how to set these, check out [Setup kernel's command-line parameters](/configuration/kernel-parameters).

## Booting & Startup

### Set root partition

- Kernel argument: `ROOT`
- Type: variable
- Values: partition or partition identifier

Set root partition. Unset to auto-detect at boot.

> Note: 
> - Partition is path to linux block device (e.g. `/dev/<device>` or `/dev/block/<device>`, both works).
> - Partition identifier is UUID or label of the filesystem on partition (e.g. `UUID=<uuid>` or `LABEL=<label>`). Currently we do not support partition UUID (`PARTUUID`) or partition label (`PARTLABEL`).

### Set slot suffix

- Kernel argument: `androidboot.slot_suffix`
- Type: variable
- Values: `_a`, `_b`

Set slot suffix for A/B mode. Unset to disable A/B mode.

### Set boot mode

- Kernel argument: `androidboot.mode`
- Type: variable
- Values: `normal` (default), `recovery`

Set boot mode.

### Set boot configuration file path

- Kernel argument: `androidboot.bootctrl_bootcfg`
- Type: variable
- Values: absolute path to file

Set boot configuration file path. Required for AB-Mode. The file path must be visible to Android system after booted (in both recovery and normal boot).

## Logging & Debugging

### Quiet mode

- Kernel argument: `quiet`
- Type: parameter

Do not show any messages during the boot process.

### Virtual console

- Kernel argument: `androidboot.enable_console`
- Type: variable
- Values: `0`, `1`

Disable/Enable virtual console on TTY. Press `Alt+F8` to open the virtual console if enabled.

### Debug shell

- Kernel argument: `DEBUG`
- Type: variable
- Values: `0`, `1`, `2`

Debug shell on boot. Set to `0` to disable. Overrides `quiet` kernel argument.

Set to `1` to launch a shell on rootfs mounted. 
Set to `2` to launch a shell on rootfs mounted and another shell after the hook scripts are executed.

On debug shell, type `exit` or press `Ctrl+D` to exit the shell.

### VSOCK

- Kernel argument: `DEBUG_VSOCK`
- Type: variable
- Values: `0`, `1`

Disable/Enable VSOCK ADB socket.

### Insecure ADB

- Kernel argument: `androidboot.insecure_adb`
- Type: variable
- Values: `0`, `1`

Disable/Enable insecure ADB socket on port 5555/TCP.

## Media codecs

### Codec2 Codecs

#### Disable Codec2

- Kernel argument: `CODEC2_LEVEL`
- Type: variable
- Values: `0`

Disable Codec2. Unset to re-enable Codec2.

### OMX Codecs

#### Disable YUV420 planar for OMX

- Kernel argument: `OMX_NO_YUV420`
- Type: variable
- Values: `1`

Disable YUV420 planar for OMX. Unset to re-enable YUV420 planar for OMX.

### FFMPEG Codecs

#### Set FFMPEG Codec2 as default

- Kernel argument: `FFMPEG_CODEC2_PREFER`
- Type: variable
- Values: `1`

Set FFMPEG Codec2 as default. Unset to disable.

#### Enable DRM Prime Handle on FFMPEG Codec2

- Kernel argument: `FFMPEG_CODEC2_DRM`
- Type: variable
- Values: `1`

Enable DRM Prime Handle on FFMPEG Codec2. Unset to disable.

#### Enable FFMPEG OMX

- Kernel argument: `FFMPEG_OMX_CODEC`
- Type: variable
- Values: `1`

Enable FFMPEG OMX. Unset to disable.

### Miscellaneous for FFMPEG

> Note: Must enable FFMPEG codecs in either c2 or OMX

#### Enable logging for FFMPEG codecs

- Kernel argument: `FFMPEG_CODEC_LOG`
- Type: variable
- Values: `1`

Enable logging for FFMPEG codecs. Unset to disable.

#### Disable hardware acceleration on FFMPEG codecs

- Kernel argument: `FFMPEG_HWACCEL_DISABLE`
- Type: variable
- Values: `1`

Disable hardware acceleration on FFMPEG codecs. Unset to re-enable.

## Disks & partitions

### NTFS options

> Note: Each represents different level of NTFS support. You can only pick one.

#### Boot with NTFS3 on a NTFS partition

- Kernel argument: `BOOT_USE_NTFS3`
- Type: variable
- Values: `1`

Enable NTFS3 on initrd. Unset to disable.

#### Set NTFS3 as default driver on vold to mount NTFS

- Kernel argument: `VOLD_USE_NTFS3`
- Type: variable
- Values: `1`

Enable NTFS3 for vold. Unset to disable.

#### Boot & Set NTFS3 as default on vold

- Kernel argument: `USE_NTFS3`
- Type: variable
- Values: `1`

Enable NTFS3 on both initrd and vold. Unset to disable.

### Miscellaneous
#### Disable SDCardFS/ESDFS bind mounting

- Kernel argument: `SDCARDFS_DISABLE`
- Type: variable
- Values: `1`

Disable SDCardFS/ESDFS bind mounting. Unset to re-enable.
#### Mount all internal partitions

- Kernel argument: `INTERNAL_MOUNT`
- Type: variable
- Values: `1`

Mount all internal partitions. Unset to disable.

## Networking

### Enable virtual wifi

- Kernel argument: `VIRT_WIFI`
- Type: variable
- Values: `1`

Enable virtual wifi. Unset to disable.

## Sensors

### Force kbd sensors

- Kernel argument: `SENSORS_FORCE_KBDSENSOR`
- Type: variable
- Values: `1`

Force use kbd sensors. Unset to disable.

### Set surfaceflinger hardware rotation

- Kernel argument: `SET_SF_ROTATION`
- Type: variable
- Values: `true`, `false`

Enable/Disable surfaceflinger hardware rotation.

### Forced orientation

- Kernel argument: `SET_OVERRIDE_FORCED_ORIENT`
- Type: variable
- Values: `true`, `false`

Enable/Disable forced orientation.

### Delay sensors load by seconds

- Kernel argument: `SENSORS_DELAY_INIT`
- Type: variable
- Values: number

Delay sensors load by seconds. Unset to disable. Recommended for tablets that uses `iio`.

## Battery

### Set fake battery level

- Kernel argument: `SET_FAKE_BATTERY_LEVEL`
- Type: variable
- Values: number (0-100)

Set fake battery level. Unset to disable.

### Set fake charging status

- Kernel argument: `SET_FAKE_CHARGING_STATUS`
- Type: variable
- Values: `1`

Set fake charging status. Unset to disable.

### Set fake battery info by AOSP

- Kernel argument: `androidboot.fake_battery`
- Type: variable
- Values: `1`

Set fake battery information by AOSP. Unset to disable.

## Power

### Set default sleep state

- Kernel argument: `SLEEP_STATE`
- Type: variable
- Values: `mem` (default), `standby`, `freeze`, `disk`

Set default sleep state. Unset to use default (`mem`).

### Turn off non-boot CPUs when suspend

- Kernel argument: `POWER_NONBOOT_CPU_OFF`
- Type: variable
- Values: `1`

Turn off non-boot CPUs when suspend. Unset to disable.

### Force max cstate level to 2 for Intel CPUs

- Kernel argument: `intel_idle.max_cstate`
- Type: variable
- Values: `2`

Force max cstate level to 2 for Intel CPUs. Recommend for some Intel Atom SoCs. Does not work on AMD CPUs. Unset to disable.

## Audio
### Set default audio HAL

- Kernel argument: `AUDIO_PRIMARY`
- Type: variable
- Values: `x86`, `x86_celadon`

Set default audio HAL controller. Unset to use default.

## Bluetooth

### Use btlinux Bluetooth HAL instead

- Kernel argument: `BTLINUX_HAL`
- Type: variable
- Values: `1`

Use btlinux Bluetooth HAL instead of default controller. Unset to disable.

### Disable Bluetooth BLE completely

- Kernel argument: `BT_BLE_DISABLE`
- Type: variable
- Values: `1`

Disable Bluetooth BLE completely. Unset to re-enable.

### Disable BLE vendor capabilities

- Kernel argument: `BT_BLE_NO_VENDORCAPS`
- Type: variable
- Values: `1`

Disable BLE vendor capabilities. Unset to re-enable.

### Set default Bluetooth UART port

- Kernel argument: `BT_UART`
- Type: variable
- Values: <!-- undocumented -->

Set default Bluetooth UART port. Unset to use auto detection.

## Graphics

### Hardware acceleration

- Kernel argument: `HWACCEL`
- Type: variable
- Values: `0`, `nomodeset`

Set to `nomodeset` to disable video driver loading only. Set to `0` to completely disable hardware acceleration. Unset to re-enable.

### EGL

- Kernel argument: `EGL`
- Type: variable
- Values: `mesa`, `angle`

#### Extra options for Mesa EGL

Applies only if `EGL` is set to `mesa`.

##### Force Mesa EGL to use llvmpipe

- Kernel argument: `MESA_LLVMPIPE`
- Type: variable
- Values: `1`

Force Mesa EGL to use llvmpipe. Unset to use default.

##### Force Mesa EGL to use Zink

- Kernel argument: `MESA_ZINK`
- Type: variable
- Values: `1`

Force Mesa EGL to use Zink. Unset to use default.

### OpenGLES

- Kernel argument: `FORCE_GLES`
- Type: variable
- Values: `2.0`, `3.0`, `3.1`, `3.2`

Set OpenGLES version. Unset to use default.

### RenderEngine

#### Force RenderEngine backend to skiagl

- Kernel argument: `FORCE_RENDERENGINE`
- Type: variable
- Values: `skiagl`

Force RenderEngine backend to skiagl. Unset to use default.

### HWC HIDL

- Kernel argument: `HWC_HIDL`
- Type: variable
- Values: `default-2.1`, `default-2.4`, `drmfb`

Set HWC HIDL interface. Unset to use default.

### HWC

- Kernel argument: `HWC`
- Type: variable
- Values: `drm`, `drm_celadon`, `drm_minigbm`, `drm_minigbm_celadon`

Set HWC interface. Unset to use default.

> Note: `HWC` can be set to `drm_minigbm` or `drm_minigbm_celadon` only when using `minigbm*` gralloc.

### Gralloc

- Kernel argument: `GRALLOC`
- Type: variable
- Values: `gbm`, `gbm_hack`, `minigbm`, `minigbm_gbm_mesa`, `minigbm_arcvm`

Set Gralloc. Unset to use default.

> Note: `GRALLOC` can be set to `minigbm_arcvm` only for VMs using `virtio-gpu` driver.

### Vulkan

- Kernel argument: `VULKAN`
- Type: variable
- Values: `lvp`, `pastel`

Set Vulkan HAL. Unset to use default.

### Celadon-specific

> Note: These options are only available if HWC is either `drm_celadon` or `drm_minigbm_celadon`

#### Enable multi-plane in HWC

- Kernel argument: `MULTI_PLANE`
- Type: variable
- Values: `1`

Enable multi-plane in HWC. Unset to disable.

#### Number of multi-plane in HWC

- Kernel argument: `MULTI_PLANE_NUM`
- Type: variable
- Values: number

Set number of multi-plane in HWC. Unset to use default.

#### Support all display modes

- Kernel argument: `HWC_PREFER_MODE`
- Type: variable
- Values: `0`

Set to `0` to support all display modes. Unset to use default.

#### Specify Connector ID

- Kernel argument: `CONNECTOR_ID`
- Type: variable
- Values: number

Specify Connector ID. Unset to use default.

#### Specify Mode ID

- Kernel argument: `MODE_ID`
- Type: variable
- Values: number

Specify Mode ID. Unset to use default.

#### Enable multi refresh rate for the system

- Kernel argument: `MULTI_REFRESH_RATE`
- Type: variable
- Values: `1`

Enable multi refresh rate for the system. Unset to disable.

### Miscellaneous

#### Force resolution on vmwgfx driver

- Kernel argument: `vmwgfx.force_resolution`
- Type: variable
- Values: resolution (`<width>x<height>`)

Force resolution on `vmwgfx` driver. Unset to use default.

#### Force resolution on virtio-gpu driver

- Kernel argument: `virtio-gpu.force_resolution`
- Type: variable
- Values: resolution (`<width>x<height>`)

Force resolution on `virtio-gpu` driver. Unset to use default.

#### Disable nouveau driver for Nvidia GPUs

- Kernel argument: `nouveau.modeset`
- Type: variable
- Values: `0`

Disable `nouveau` driver for Nvidia GPUs. Unset to re-enable.

## Recovery

### ADB port

- Kernel argument: `DEBUG_NET_PORT`
- Type: variable
- Values: number

Set ADB port. Unset to use default.

### Force default color format

- Kernel argument: `androidboot.pixel_format`
- Type: variable
- Values: `RGBX_8888`, `ABGR_8888`, `ARGB_8888`, `BGRA_8888`, `RGBA_8888`, `BGRX_8888`

Force default color format. Unset to use default.

## Camera

### Emulated camera

- Kernel argument: `EMULATED_CAMERA`
- Type: variable
- Values: `1`

Enable emulated camera. Unset to disable.

## Miscellaneous

### Set default DPI

- Kernel argument: `DPI`
- Type: variable
- Values: number

Set default DPI. Unset to use default.

### Force disable Setup Wizard

- Kernel argument: `SETUPWIZARD`
- Type: variable
- Values: `0`

Force disable Setup Wizard. Unset to use default.

### Enable PC Mode

- Kernel argument: `PC_MODE`
- Type: variable
- Values: `1`

Enable PC Mode. Unset to disable.

### Enable HPE Mode

- Kernel argument: `HPE_MODE`
- Type: variable
- Values: `1`

Enable HPE Mode. Unset to disable.

### Disable x86 syscall hardening

- Kernel argument: `syscall_hardening`
- Type: variable
- Values: `off`

Disable x86 syscall hardening. Useful if you are using KernelSU. Unset to enable.