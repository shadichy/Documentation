---
order: 3
---

# Hardware Requirements

## The requirements

To be able to use BlissOS, you must have a CPU that supports at least x86_64-v2 [Microarchitecture level](https://en.wikipedia.org/wiki/X86-64#Microarchitecture_levels). This version match with [x86_64 Android ABI requirement](https://developer.android.com/ndk/guides/abis) so your CPU must be compatible or else it won't be able to boot at all.

The rest of the requirement are follow:

Minimum (assuming you are using BlissOS Go):

```
CPU : Any x86_64-v2+ compatible CPU
RAM : 1.5 GB minimum
GPU : Any, but highly recommend Intel or AMD
Storage : 20 GB or higher
```

Recommended:

```
CPU : Any x86_64-v2+ compatible CPU
RAM : 8 GB minimum
GPU : Any, but highly recommend Intel or AMD
Storage : 64 GB or higher
```

## Knowing if your CPU supported x86_64-v2

To know if your CPU support x86_64-v2, you can check your CPU features using programs like [CPUID's CPU-Z](https://www.cpuid.com/softwares/cpu-z.html). 

On Linux, there are scripts that can easily check and report it to you. For example [x86-64-level](https://github.com/HenrikBengtsson/x86-64-level). Or else you can just use `cat /proc/cpuinfo` and compare the flags with the required instructions.

You can also search for your CPU at [CPU-World](https://www.cpu-world.com/) or [TechPowerUp's CPU database](https://www.techpowerup.com/cpu-specs/).

## A list for specific hardware

We have a list of tested hardware that is currently maintaining by us & the community. Check [BlissOS Compatibility List](compatibility-list.md) for more info.
