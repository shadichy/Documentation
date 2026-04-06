# Mesa Configuration

The Mesa graphics stack is highly adaptable, offering a wide array of configuration options to fine-tune rendering behavior, workaround application bugs, or optimize performance. This section details how to apply these custom configurations across your BlissOS system.

## DRIconf Overrides

!!! info "What is DRIconf?"
    [**DRIconf**](https://dri.freedesktop.org/wiki/DRIconf/) (DRI Configuration) is Mesa's solution for applying application-specific or driver-specific workarounds. Instead of modifying system-wide environment variables, DRIconf uses XML files to define rules that tell the graphics driver how to behave when a specific condition is met (like a particular game launching or a specific GPU being detected).

**Where to find available options:**
Mesa developers frequently add new workarounds. You can find the complete, up-to-date list of available `driconf` options in the Mesa source code under [`src/util/driconf.h`](https://gitlab.freedesktop.org/mesa/mesa/-/blob/main/src/util/driconf.h).

### Applying Rules (Global vs. Driver-Specific)

When writing a `driconf` file, you can target a specific driver or apply the rule globally to any Mesa driver loaded by the system.

**1. Driver-Specific Configuration:**
If you want to apply a rule *only* when a specific driver is being used, specify the driver name in the `<device>` tag. This applies to both OpenGLES drivers (for example `radeonsi` for AMDGPUs) and Vulkan drivers (for example `anv` for Intel GPUs).

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE driconf [
   <!ELEMENT driconf      (device+)>
   <!ELEMENT device       (application | engine)+>
   <!ATTLIST device       driver CDATA #IMPLIED
                          device CDATA #IMPLIED>
   <!ELEMENT application  (option+)>
   <!ATTLIST application  name CDATA #REQUIRED
                          executable CDATA #IMPLIED
                          executable_regexp CDATA #IMPLIED
                          sha1 CDATA #IMPLIED
                          application_name_match CDATA #IMPLIED
                          application_versions CDATA #IMPLIED>
   <!ELEMENT engine       (option+)>

   <!-- engine_name_match: A regexp matching the engine name -->
   <!-- engine_versions: A version in range format
             (version 1 to 4 : "1:4") -->

   <!ATTLIST engine       engine_name_match CDATA #REQUIRED
                          engine_versions CDATA #IMPLIED>

   <!ELEMENT option       EMPTY>
   <!ATTLIST option       name CDATA #REQUIRED
                          value CDATA #REQUIRED>
]>

<driconf>
    <device driver="radeonsi">
        <application name="Some Buggy Game" executable="com.somecompany.some_bug_game">
            <option name="allow_glsl_extension_directive_midshader" value="true" />
        </application>
    </device>
</driconf>
```

**2. Global Configuration:**
If you want the rule to apply regardless of what GPU the system is running, leave the driver attribute blank or omit it to match all devices.

```xml
<driconf>
    <device>
        <application name="Universal Emulation Fix" executable="com.something.emufix">
            <option name="mesa_glthread" value="false" />
        </application>
    </device>
</driconf>
```

### Where to Save DRIconf Files

Traditionally, Linux distributions place these files in `/etc/drirc` or `/usr/share/drirc.d/`. However, on Android, the `/system` and `/vendor` partitions are read-only. 

To make configuration easy for users, BlissOS supports reading `driconf` files directly from the `/data` partition. You do not need to remount your system as read-write.

Save your custom configuration files here:
`/data/vendor/drirc.d/*.conf`

!!! tip
    Mesa loads these files in alphabetical order. If you want to ensure your custom rules override the built-in system rules, name your file starting with a high number, for example: `/data/vendor/drirc.d/99-custom-tweaks.conf`.

### Spoofing OpenGLES driver info using driconf

Many Android games and applications query the OpenGL ES renderer string (`GL_VENDOR` and `GL_RENDERER`) to determine which graphics settings to unlock. If a game does not recognize your PC's GPU (for example, if it sees "Mesa" or "Intel"), it may lock you into the lowest graphics settings, disable advanced visual effects, or refuse to run entirely.

You can spoof these strings using `driconf` to trick the application into thinking it is running on a high-end mobile GPU, such as a Qualcomm Adreno or ARM Mali.

**Key Options:**

* `force_gl_vendor` (string): Overrides the reported vendor name.
* `force_gl_renderer` (string): Overrides the reported renderer name.

**Example: Spoofing an Adreno 640 for a specific game**

```xml
<driconf>
    <device>
        <application name="Mobile Game" executable="com.tencent.ig">
            <option name="force_gl_vendor" value="Qualcomm" />
            <option name="force_gl_renderer" value="Adreno (TM) 640" />
        </application>
    </device>
</driconf>
```

### Spoofing Vulkan driver info using driconf

Many modern Android games (such as [*Gakuen iDOLM@STER*](https://gakuen.idolmaster-official.jp/)) check Vulkan's `deviceName` or `vendorID` against a strict hardware whitelist to determine if the hardware is capable of running the highest graphics settings. If your PC's GPU name is not recognized, you may be locked into the lowest visual tiers. To deal with cases like this, newer Mesa versions allow spoofing the `vendorID`, and BlissOS includes a custom feature to spoof the `deviceName`.

**Key Vulkan Overrides:**

* `force_vk_vendor` (integer): Overrides the Vulkan `vendorID`.
* `force_vk_devicename` (string): Overrides the Vulkan `deviceName`. 

!!!info 
    `force_vk_devicename` is a custom feature currently [under review](https://gitlab.freedesktop.org/mesa/mesa/-/merge_requests/29629) for upstream Mesa, but is already fully integrated into BlissOS across drivers like `anv`, `hasvk`, `radv`, `nvk`, `venus`, and `lvp`

**Example: Spoofing a Snapdragon (Adreno) GPU and 8GB VRAM**

In this example, we trick a game into thinking it is running on a high-end mobile chip to unlock maximum settings.

```xml
<driconf>
    <device>
        <application name="Strict Whitelist Game" executable="com.test.game">
            <option name="force_vk_vendor" value="20803" />
            <option name="force_vk_devicename" value="Adreno (TM) 740" />
        </application>
    </device>
</driconf>
```


## Android System Properties

If you are coming from a traditional Linux environment like Arch Linux, you are likely used to controlling Mesa and Gallium driver behavior by exporting environment variables (e.g., `export LIBGL_DEBUG=verbose`). However, within the AOSP ecosystem, Mesa handles this differently. 

On BlissOS, Mesa automatically translates standard environment variables into **Android system properties**. 

You can view the full list of available Mesa variables in the official [Mesa Environment Variables](https://docs.mesa3d.org/envvars.html). To apply any of these on BlissOS, follow Mesa's [Android System Properties](https://docs.mesa3d.org/android.html#android-system-properties) to know the rules of the property you're going to set.

**Example:** If you want to use the `LIBGL_DEBUG` environment variable, the translation process makes it `debug.mesa.libgl.debug`.

You can set and query these from a root shell or ADB using standard Android commands:

```sh
# Set the property
setprop debug.mesa.libgl.debug verbose

# Verify it was set
getprop debug.mesa.libgl.debug
```

!!! tip "Fun fact"
    The `FORCE_GLES` kernel parameter that is explained in [Graphics options explanation](../../knowledgebase/graphics-explain.md#force_gles) does sets the `mesa.gles.version.override` property at boot to enforce a specific OpenGLES version.
