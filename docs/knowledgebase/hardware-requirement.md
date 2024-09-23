---
order: 3
---

# Hardware requirements

## The requirements

To be able to use BlissOS, you must have a CPU that supports at least `x86_64-v2` [Microarchitecture level][Microarchitecture level]. This version match with [x86_64 Android ABI requirement][x86_64 Android ABI requirement] so your CPU must be compatible or else it won't be able to boot at all.

!!!warning

    On [Android ABIs][Android ABIs] page, the note said that this is `x86-64-v1 only` which is actually misleading. Only `x86_64-v2` and above have the required instructions for running Android on x86_64 devices for example SSE4.2 !

The rest of the requirement are follow:

Minimum (assuming you are using BlissOS Go):

```
CPU : Any `x86_64-v2+` compatible CPU
RAM : 1.5 GB minimum
GPU : Any, but highly recommend Intel or AMD
Storage : 20gb or higher
```

Recommended:

```
CPU : Any `x86_64-v2+` compatible CPU
RAM : 8 GB minimum
GPU : Any, but highly recommend Intel or AMD
Storage : 64gb or higher
```

## Knowing if your CPU supported `x86_64-v2`

To know if your CPU support `x86_64-v2,` you can check your CPU features using programs like [CPUID's CPU-Z][CPUID's CPU-Z].

On Linux, there are scripts that can easily check and report it to you. For example [x86-64-level][x86-64-level].
Another one is using the command `/lib/ld-linux-x86-64.so.2 --help | grep supported`, simple and easy.
Or else you can just use `cat /proc/cpuinfo` and compare the flags with the required instructions.

You can also search for your CPU at [CPU-World][CPU-World] or [TechPowerUp's CPU database][TechPowerUp's CPU database].

## A list for specific hardware

We have a list of tested hardware that is currently maintaining by us & the community. Check [BlissOS Compatibility List][BlissOS Compatibility List] for more info.


[Microarchitecture level]: https://en.wikipedia.org/wiki/X86-64#Microarchitecture_levels
[x86_64 Android ABI requirement]: https://developer.android.com/ndk/guides/abis
[Android ABIs]: https://developer.android.com/ndk/guides/abis
[CPUID's CPU-Z]: https://www.cpuid.com/softwares/cpu-z.html
[x86-64-level]: https://github.com/HenrikBengtsson/x86-64-level
[CPU-World]: https://www.cpu-world.com/
[TechPowerUp's CPU database]: https://www.techpowerup.com/cpu-specs/
[BlissOS Compatibility List]: compatibility-list.md
