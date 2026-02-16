# Explanation for BlissOS' graphics options

BlissOS inherits its core graphics stack implementation from the Android-x86 project. To ensure broad compatibility across the x86 ecosystem, several graphics initialization modes are available during boot. This section details the purpose of each graphics option and documents the specific values they accept.

!!! warning
    All of the options listed below are **kernel parameters**. If you are unsure how to apply them, please refer to our guide on [Setting up kernel command-line parameters](../configuration/kernel-parameters.md).

## EGL

!!! info "What is EGL?"
    According to [Wikipedia](https://en.wikipedia.org/wiki/EGL_(API)), "*EGL is an interface between Khronos rendering APIs (such as OpenGL, OpenGL ES or OpenVG) and the underlying native platform windowing system.*" Android uses EGL to handle graphics context management. For a deeper dive, check out the [Android Graphics Architecture](https://source.android.com/docs/core/graphics/arch-egl-opengl) page.

The following `EGL` options are available in BlissOS to handle different graphics rendering paths:

* **`angle`**: Uses Google's [ANGLE](https://github.com/google/angle) (Almost Native Graphics Layer Engine). This option translates OpenGL ES 2.0/3.0/3.1 calls into Vulkan commands. This mode requires a working [Vulkan HAL](#vulkan) to function.
    * BlissOS utilizes the built-in ANGLE provided by the upstream AOSP source.

* **`mesa`** *(Default)*: Uses [Mesa3D's EGL](https://docs.mesa3d.org/egl.html). Alongside [Mesa3D's Gallium](https://gallium.readthedocs.io/en/latest/gallium/intro.html#what-is-gallium) drivers, this provides the standard hardware acceleration for supported GPUs, as well as software fallback options.
    * BlissOS maintains a [custom Mesa fork](https://github.com/android-generic/external_mesa) to ensure the best compatibility and performance for the specific hardware we support.

* **`swiftshader`**: Uses Google's [SwiftShader](https://github.com/google/swiftshader). This is a purely CPU-based software renderer for OpenGL ES.
    * **Deprecated in BlissOS 16+ (Android 13+).** Newer versions favor `SwANGLE` (SwiftShader + ANGLE), where ANGLE translates GLES to Vulkan, and SwiftShader runs the Vulkan implementation on the CPU.

## GRALLOC

!!! info "What is Gralloc?"
    **Gralloc** (Graphics Allocator) is the Android HAL module responsible for allocating graphics memory buffers. It acts as the middleman between the graphics drivers and the Android framework, deciding how memory is allocated for surfaces like app windows, video frames, and game rendering.

    **Read More:**
    
    * [Android Graphics Architecture (Official AOSP)](https://source.android.com/docs/core/graphics) - A high-level overview of how surfaces and buffers work.
    * [BufferQueue and Gralloc](https://source.android.com/docs/core/graphics/arch-bq-gralloc) - Technical details on the Gralloc interface.

BlissOS includes two primary Gralloc implementations, with several specific forks to handle the wide variety of PC hardware and specific driver quirks. The system automatically selects the best default based on your GPU, but these can be manually overridden.

### gbm
This is the [gbm_gralloc](https://github.com/robherring/gbm_gralloc) implementation. BlissOS maintains a fork of this project at [android-generic/external_gbm_gralloc](https://github.com/android-generic/external_gbm_gralloc).

* **`gbm`**: The standard `gbm_gralloc`. It uses the Mesa3d's libgbm API to allocate buffers.
* **`gbm_hack`**: A modified version containing a specific workaround: [*"HACK: Use pixel stride instead of width as gbm->map() argument"*](https://github.com/android-generic/external_gbm_gralloc/commit/e70acfc3f5acc64168a172987f34d141e55f6b54). This was historically used to fix broken graphics rendering on Mesa's `iris` (Intel) or `nouveau` (Nvidia) drivers before better solutions were found.
* **`gbm_noscanout`**: A version with the "scanout" code path removed. By removing the ability for Gralloc to send buffers directly to the display, it forces the Hardware Composer (HWC) to handle the rendering instead.
    * This was a workaround for `iris` driver issues before the `gbm_hack` solution was implemented.

### minigbm
[Minigbm](https://android.googlesource.com/platform/external/minigbm/) is a lightweight graphics buffer allocator originally developed for ChromeOS. BlissOS maintains a fork at [android-generic/external_minigbm](https://github.com/android-generic/external_minigbm).

* **`minigbm`**: The default Minigbm implementation. It supports a wide range of backends including `virtio_gpu`, `i915`, `xe`, `amdgpu`, `nouveau`, and `vmwgfx`.
* **`minigbm_gbm_mesa`**: A specialized backend created by [Roman Stratiienko](https://github.com/rsglobal).
    * This effectively adapts [gbm_gralloc](#gbm) to run *inside* Minigbm as a backend. It leverages Mesa3D's libgbm APIs directly, aiming for optimal allocation on any hardware Mesa supports.
* **`minigbm_arcvm`**: The specific Minigbm configuration used in ChromeOS's ARCVM (Android Runtime for Chrome Virtual Machine). This backend is designed specifically for virtualized environments. It is optimized to work with `virtio-gpu` to efficiently share graphics buffers between the host system and the Android guest VM.
    * By default BlissOS inside a virtual machine (QEMU/KVM) with virtio-gpu enabled will use this option.

### GRALLOC4_MINIGBM

This is a specialized boot option that switches the version of the Gralloc and Mapper services used by the system.

* **`GRALLOC4_MINIGBM=1`**: Forces the system to use the **Gralloc 4.0** and **Mapper 4.0** services provided by Minigbm, instead of the default Gralloc 2.0 / Mapper 2.0 implementation.

!!! warning
    This option **must only** be used when `GRALLOC` is set to a `minigbm` variant (e.g., `minigbm`, `minigbm_gbm_mesa`, etc.). 
    
    Other allocators like `gbm_gralloc` do not support the service structure required for this mode. Enabling this flag with an incompatible allocator will likely result in a broken graphics stack or boot failure.

## HWC

!!! info "What are HWC and Composer HIDL Services?"
    **Hardware Composer (HWC):** The HWC is a Hardware Abstraction Layer (HAL) that determines the most efficient way to composite buffers (layers of graphics) on your screen. Instead of forcing the GPU to draw everything (which drains battery and impacts performance), the HWC offloads the composition of layers (like the status bar, apps, and navigation bar) directly to the display controller hardware. 
    [Read more about HWC](https://source.android.com/docs/core/graphics/implement-hwc)

    **Composer HIDL Service (HWC_HIDL):** HIDL (Hardware Interface Definition Language) is the system Android uses to allow the core OS framework (like `SurfaceFlinger`) to communicate with hardware drivers. The "Composer HIDL Service" is the actual background daemon that hosts the HWC implementation and translates commands between the OS and the graphics stack.
    [Read more about HIDL](https://source.android.com/docs/core/architecture/hidl)

Just like [GRALLOC](#gralloc), BlissOS automatically selects the best `HWC` and `HWC_HIDL` configurations based on the detected hardware. However, these can be manually overridden using the boot parameters below.

The `HWC=` parameter defines which Hardware Composer implementation to load.

* **`default`**: Uses the standard, dummy fallback hwcomposer provided by AOSP.
* **`drm*` variants**: These use `drm_hwcomposer`, which utilizes standard Linux DRM/KMS APIs to handle display composition. BlissOS maintains a fork at [android-generic/external_drm_hwcomposer](https://github.com/android-generic/external_drm_hwcomposer). The `_celadon` suffixes pull from [a specific branch](https://github.com/android-generic/external_drm_hwcomposer/tree/celadon) containing patches from Intel's [Project Celadon](https://github.com/projectceladon).
    * **`drm`** / **`drm_celadon`**: The standard `drm_hwcomposer` implementations. **These should be paired with [gbm_gralloc](#gbm)**.
    * **`drm_minigbm`** / **`drm_minigbm_celadon`**: Uses `drm_hwcomposer` compiled with the legacy Minigbm `BufferInfoGetter`. This allows the composer to properly read the proprietary buffer metadata generated by Minigbm. **Must be paired with [minigbm](#minigbm).**
    * **`drm_gbm_cros`** / **`drm_gbm_cros_celadon`**: Uses `drm_hwcomposer` with a legacy `gbm_gralloc` BufferInfo getter that mimics the ChromeOS (CrOS) API. 
        * *Context:* The BlissOS `gbm_gralloc` fork implements the [CrOS API to retrieve buffer info](https://github.com/BlissRoms-x86/gbm_gralloc/commit/1ad5fd5a226cc2c32103e351ecf2404aaa9216d2), and this HWC option utilizes an [extra bufferinfo file](https://github.com/android-generic/external_drm_hwcomposer/commit/45a97c66aff45103959d2b55e05d89aec75b75dd) to read it. **Must only be used by [gbm_gralloc](#gbm).**

### HWC_HIDL

The `HWC_HIDL=` parameter specifies which version of the Composer HIDL service daemon should be used to host the chosen HWC implementation.

* **`default-2.1`**: Uses the `android.hardware.graphics.composer@2.1-service`. 
    * This is typically paired with `HWC=default` to handle software-rendering fallback environments (no hardware acceleration) or with [gbm_gralloc](#gbm) when using the generic [drm_framebuffer](https://github.com/android-generic/external_drm_framebuffer) wrapper.
* **`default-2.4`**: Uses the `android.hardware.graphics.composer@2.4-service`.
    * This is the modern service and should be used whenever `drm_hwcomposer` (`HWC=drm*`) is selected, as it supports newer display features.
* **`drmfb`**: Uses a custom `drmfb-composer` service (`android.hardware.graphics.composer@2.1-service.drmfb`) originally developed by [me176c-dev](https://github.com/me176c-dev/drmfb-composer).
    * This option should be used *without* specifying an `HWC=` parameter. It allows booting with [minigbm](#minigbm) or [gbm_gralloc](#gbm) by drawing directly to the Linux framebuffer, completely bypassing traditional hardware composition.

## VULKAN

!!! info "What is Vulkan?"
    **Vulkan** is a modern, cross-platform 3D graphics API designed to minimize CPU overhead and give developers more direct control over the GPU. Unlike OpenGL, which manages a lot of complexity for the developer, Vulkan is explicit, allowing for better performance in multi-core systems.

    **Read More:**
    
    * [Vulkan on Android (Official AOSP)](https://source.android.com/docs/core/graphics/arch-vulkan)
    * [What is Vulkan? (Khronos Group)](https://www.vulkan.org/)

!!! info "The History of VULKAN=1"
    In the early days of Android-x86, the boot option `VULKAN=1` was used to force the system to switch from the legacy [drm_gralloc](https://android.googlesource.com/platform/external/drm_gralloc/) (which did not support Vulkan) to [gbm_gralloc](#gbm).

    As development progressed, [drm_gralloc](https://android.googlesource.com/platform/external/drm_gralloc/) was deprecated and removed, leaving [gbm_gralloc](#gbm) and [minigbm](#minigbm) as the standards—both of which support Vulkan out of the box. Consequently, the old `VULKAN=1` flag became obsolete.

    **However**, BlissOS has recently reintroduced the `VULKAN=` option with a new purpose: **switching between specific Vulkan HALs (drivers).**

Realistically, the correct Vulkan HAL is **automatically selected** based on your hardware. You should generally only use these options if you need to debug a specific issue or force a fallback.

The `VULKAN=` parameter accepts the following values:

**Hardware Drivers:**

* **`intel`**: Loads the [ANV](https://docs.mesa3d.org/drivers/anv.html) driver. This is the modern Mesa Vulkan driver for Intel GPUs (Skylake and newer).
* **`intel_hasvk`**: Loads the [HasVK](https://www.phoronix.com/news/Intel-ANV-HASVK-Split-Merged) driver. This is the legacy Mesa driver split off for older Intel architectures (Haswell, Broadwell).
* **`radeon`**: Loads the [RADV](https://docs.mesa3d.org/drivers/radv.html) driver. This is the standard, high-performance Vulkan driver for AMD Radeon GPUs.
* **`nouveau`**: Loads the [NVK](https://docs.mesa3d.org/drivers/nvk.html) driver for NVIDIA GPUs.
* **`virtio`**: Loads the [Venus](https://docs.mesa3d.org/drivers/venus.html) driver. This is for virtualized environments (QEMU/KVM) where the host passes Vulkan capabilities to the guest.

**Software Rendering (CPU-based):**

* **`pastel`**: Forces the use of [SwiftShader](https://github.com/google/swiftshader) Vulkan.
* **`lvp`**: Forces the use of [LLVMpipe (Lavapipe)](https://docs.mesa3d.org/drivers/llvmpipe.html). This is Mesa's CPU-based Vulkan implementation.

!!! tip "Recommendation"
    For 99% of users, **do not set a `VULKAN=` value.** BlissOS's auto-detection logic mimics the behavior of standard Linux distributions to pick the best driver for your card. Use `pastel` or `lvp` only if your GPU is unsupported or crashing.

## FORCE_GLES

!!! info "What is OpenGL ES?"
    **OpenGL ES** (Embedded Systems) is a subset of the OpenGL graphics API designed specifically for mobile and embedded devices like smartphones. It is the primary API Android uses to render 3D graphics.
    
    Different apps require different versions of OpenGL ES to run. For example, a simple 2D game might only need **ES 2.0**, while a high-end 3D game might demand **ES 3.2** for advanced lighting and tessellation.

    **Read More:**

    * [OpenGL ES Overview (Khronos Group)](https://www.khronos.org/opengles/)
    * [Android OpenGL ES Guide](https://developer.android.com/guide/topics/graphics/opengl)

This boot option forces the system to report a specific OpenGL ES version to applications and the graphics driver. This is useful for testing compatibility or fooling older apps that might not recognize newer driver versions.

* **`FORCE_GLES=<version>`**: Sets the reported OpenGL ES version.
    * **Supported Values:** `2.0`, `3.0`, `3.1`, `3.2`.

**Default Behavior:**
If this option is not set, BlissOS defaults to reporting **OpenGL ES 3.0** (Internal value: `196608`).

!!! info "Technical Implementation"
    When you set this option, the system modifies two key properties during boot:

    1.  **`ro.opengles.version`**: The Android system property that tells apps what the device supports (corresponds to `<uses-feature>` in an app's Manifest).
    2.  **`mesa.gles.version.override`**: Sets the `MESA_GLES_VERSION_OVERRIDE` environment variable, which forces the Mesa driver to technically report the chosen version, even if the hardware supports more (or less).

    | Option Value | `ro.opengles.version` (Integer) | `ro.opengles.version` (Hex) |
    | :--- | :--- | :--- |
    | **2.0** | `131072` | `0x00020000` |
    | **3.0** | `196608` | `0x00030000` |
    | **3.1** | `196609` | `0x00030001` |
    | **3.2** | `196610` | `0x00030002` |

## HWACCEL

This option controls the hardware acceleration state of the Android graphics stack.

* **`HWACCEL=0`**: Disables hardware acceleration and forces the system to use software rendering.

!!! info "Under the Hood"
    When you set `HWACCEL=0`, the system overrides several other graphics options to ensure a stable software-only environment. Specifically, it configures the stack as follows:

    * **Gralloc / HWC:** Sets both to `default` (using the AOSP fallback implementations).
    * **HWC Service:** Sets `HWC_HIDL=default-2.1`.
    * **Rendering:** Sets `VULKAN=pastel` (SwiftShader) and `EGL=angle`.
        * This activates the **SwANGLE** software rendering path (ANGLE translating GLES to Vulkan, running on the SwiftShader CPU driver).

!!! info "Difference from nomodeset"
    It is common to confuse `HWACCEL=0` with the Linux kernel parameter `nomodeset`, but they function at different levels of the system:

    * **`HWACCEL=0` (User Space):** The kernel video drivers (like `i915` or `amdgpu`) **still load**, allowing for proper screen resolution and brightness control. However, Android is told to ignore the GPU for 3D rendering and compositing, doing all the work on the CPU instead.
        * *Use this if:* You have graphical glitches or artifacts but the system boots fine.

    * **`nomodeset` (Kernel Space):** This instructs the Linux kernel to **not load** video drivers at all. The system falls back to basic BIOS/UEFI display modes (often with wrong resolution and no brightness control).
        * *Use this if:* The system freezes or black screens *before* the Android boot animation appears.
        * Read about nomodeset on [ArchLinux Wiki](https://wiki.archlinux.org/title/Kernel_mode_setting#Disabling_modesetting) or [Linux Kernel Documentation](https://docs.kernel.org/admin-guide/kernel-parameters.html)

## FORCE_RENDERENGINE

!!! info "What is RenderEngine?"
    **RenderEngine** in Android is a low-level component within SurfaceFlinger that draws layers, handles shadows, and composites graphics buffers, typically utilizing OpenGL ES or SkiaGL to render final images for the screen. 

    **Read More:**

    * [tm23forest's Android surfaceflinger RenderEngine explained](https://tm23forest.com/contents/android-surfaceflinger-renderengine-explained)

This boot option forces SurfaceFlinger to use a specific graphics backend for its RenderEngine operations. It works by setting the system property `debug.renderengine.backend`.

* **`FORCE_RENDERENGINE=<value>`**: overrides the default backend.

### Supported Values

**Modern Backends ([Skia](https://skia.org/)):**

* **`skiavk`**: Uses the **Skia** graphics library with a **Vulkan** backend (non-threaded).
* **`skiavkthreaded`**: Uses the **Skia** graphics library with a **Vulkan** backend (threaded).
    * `skiavk` & `skiavkthreaded` were introduced in Android 14. This is the modern standard for devices with stable Vulkan drivers.
* **`skiagl`**: Uses the **Skia** graphics library with an **OpenGL ES** backend (non-threaded).
* **`skiaglthreaded`**: Uses the **Skia** graphics library with an **OpenGL ES** backend (threaded).
    * Default for BlissOS 16+ (Android 13+).

**Legacy Backends (GLES):**

* **`threaded`**: Uses the legacy **GLES** implementation with threading enabled.
    * Default for BlissOS 14 & 15 (Android 11/12).
* **`gles`**: Uses the legacy GLES implementation *without* threading.

## MESA_LLVMPIPE and MESA_ZINK

These options allow you to force specific drivers within the Mesa3D stack.

!!! warning "Requirement"
    These options **must** be used in conjunction with `EGL=mesa`. 
    
    If you are using `EGL=angle` or `EGL=swiftshader`, these settings will be ignored.

### MESA_LLVMPIPE

!!! info "What is LLVMpipe?"
    **LLVMpipe** is a high-performance, CPU-based software rasterizer for OpenGL. It uses the LLVM compiler infrastructure to translate graphics commands into CPU instructions.
    
    In the Android ecosystem, its closest equivalent is [**SwiftShader**](https://github.com/google/swiftshader). However, while SwiftShader is Google's implementation, LLVMpipe is the standard software fallback for Linux desktops. It is generally very accurate but CPU-intensive, making it a reliable choice for debugging when hardware acceleration fails.
    
    **Read More:** [Mesa3D LLVMpipe Driver](https://docs.mesa3d.org/drivers/llvmpipe.html)

This option forces the system to use **LLVMpipe**, Mesa's software rasterizer, by setting the property `mesa.libgl.always.software=true`.

* **`MESA_LLVMPIPE=1`**: Forces software rendering.

### MESA_ZINK

!!! info "What is Zink?"
    **Zink** is a Mesa driver that implements OpenGL by translating everything into Vulkan commands. This allows devices with a working Vulkan driver (but a poor or missing OpenGL driver) to run full hardware-accelerated OpenGL.
    
    This concept is very similar to Google's [**ANGLE**](https://github.com/google/angle) project. You might want to use it as an alternative to ANGLE.
    
    **Read More:** [Mesa3D Zink Driver](https://docs.mesa3d.org/drivers/zink.html)

This option forces the system to use **Zink**, Mesa's OpenGL-over-Vulkan implementation, by setting the property `mesa.loader.driver.override=zink`.

* **`MESA_ZINK=1`**: Forces OpenGL commands to be translated into Vulkan commands.
