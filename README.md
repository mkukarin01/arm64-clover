# arm64-clover & me
__Important__: This page is just a bunch of thoughts about bringing native armv8 OS X/mac OS Big Sur (I don't know anything about real M1 chip and type of binaries that works on real hardware) to custom ARM development hardware such as [Socionext](https://www.96boards.org/product/developerbox/). At this moment I'm just guessing.

__Motivation__: I'm just curious enough to take this challenge and want review my knowledge about hardware and OS'es. Extra part: I want to have a real argument in discussions about performance of new M1 chip produced by Apple and put my own dot in holywars "x86 vs ARMv8".

__what'a'name__: As Slice mentioned (author of original Clover Bootloader) – "It's your project, so deal with it on your own". Maybe it's some kind of respect, IDK.

# Legal notes
I fully understand that "Hackintosh" and anything related is absolutely Illegal. But as mentioned in [Dortania's Guides](https://dortania.github.io/OpenCore-Install-Guide/why-oc.html#legality-of-hackintoshing) hackintosh itself is not absolutely illegal in case of study and personal use.

So, I am putting this as my thoughts with no warranty and so on under BSD-2-Clause License and for academic purposes in reverse engineering and OS studies.

# Start
So, every guide about building hackintosh is starting from the point "Know your hardware", its really solid approach not only for Intel/AMD based platforms, but although for me.

## Original Apple hardware by model: MacBookAir10.1, MacBookPro17.1, Macmini9,1
I'll go over the components of SoC M1, in the order that is essential to boot the entire operating system.

### CPU
All devices from the previous headline share the same SoC - Apple M1, in different configurations and components (RAM, GPU), but all of them [use](https://en.wikipedia.org/wiki/Apple_M1) ARMv8-A instructions set. SoC is a black box which contains in our case CPU, GPU, RAM and additional ICs. In addition, CPU themself configuration similar to [ARM big.LITTLE](https://en.wikipedia.org/wiki/ARM_big.LITTLE) or Intel's Lakefield processors. Maybe, I should mark this point as an issue that puts a big tombstone on the whole idea, but in my opinion it's okay. Why? A lot of hackintosh builds are based on new AMD CPUs like Ryzen with Zen arch and so on with almost [native support](https://github.com/AMD-OSX/AMD_Vanilla) of new CPUs. In some cases it's absolutely absolutely worthless, in others good enough. In conclusion my point is "If CPU uses based on the same arch and instructions set the XNU core would be able to start in native way as it started on M1 SoC".

### GPU
Oh, that thing is a tough one. M1 uses an integrated GPU, which has proprietary kernel extensions (kext, just guessing) to boot OS's GUI. That part goes beyond XNU Kernel and is about to bring real troubles. So, if I want to use macOS on custom hardware with all fancy things, I must be able to get GPU working (using XNU in vanilla way on ARM is not a big deal, download open sourced version from Apple and try to boot your Raspberry Pi with this software). In the good old days you probably patch your Intel Iris Pro via Clover and wow, it's working, with native OS's kext. At now days, I suppose it would possible if:

0. Apple released external GPU support,
1. Someone at Apple built old Nvidia or AMD Radeon kexts for the new platform ('cause old kexts work on x86 and now it armv8, so AFAIK this binaries are different).

At this point I should pray and find guides “how to build your brand new kext”. Or, port some of kexts from old OS’s internals and use weird combinations of Rosetta2 translation layer and old kext (if it's possible).

### RAM
Not a big deal, in my opinion. LPDDR4X – is a common type at now days of RAM, and I don't remember that RAM causes of kernel panics in old OS's

### Apple ICs
Neural Engine, Image Signal Processor, NVMe storage controller, Thunderbolt 4 controller. It's important ICs for working with computers, but if I could have a USB Mass Storage device support it's enough to start. As the reason for this text is just compare M1 CPU with other ARM CPU, emulating or bringing alternative devices is not important. But it would be a great deal if I could use regular SATA HDD.

## Not original hardware
My favorite part, get some stuff and do something fun. Nowadays, there is a big pile of ARM boards all around the world: single board computers (SBC), development kits and barebones from OEMs. For strict order let's discuss them separately.

### SBC
They are pure and beautiful like a wind at a temperature far-far below zero. So, in this contest of my frankensteins, I'll mention only a few of them, 'cause not all hardware is compatible and providing all stuff that needs to start.

* [Raspberry PI 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/specifications),
* [HiKey](https://www.96boards.org/product/hikey/)

At this moment and in case of SBC, I'm ignoring everything about specs, sizes and etc, the main clue for this educational project is a board capable of working with EDK2 or simply having UEFI. AFAIK there is [UEFI ACPI Working Group (ASWG)](https://uefi.org/workinggroups) that should work on a new standard for ARM based boards for UEFI. You may ask what-a-holy-moly happened here, but as I guess UEFI and EDK2 is part of a big unification process, as that happened at x86, so you may use any ATX power supply with your motherboard or any new OS with default UEFI env. 

In good addition, possibility to add external GPU even with PCIe x1. Its common use-case for older GPUs (Nvidia 1070 and closest relatives, AMD RX 5xx) to work with slow PCI lanes.

Boards which I won't target in some cases: 
* [Khadas VIM 3](https://www.khadas.com/vim3) – no acpi tree uefi, only uboot efi-for-grub [implementation](https://forum.khadas.com/t/uefi-plans-for-the-vim3/7148), but PCI-e without a pain (ha-ha, just joking, without soldering it will never work).
[WIP]

#### Raspberry PI 4
[Experimental repo](https://github.com/pftf/RPi4) – was built on official EDK2, and provides nothing but an UEFI env for booting any OS.

In addition, r-pi 4 could be patched to get access to the PCIe lane (sacrificing the VL805 USB 3.0 controller chip in the process) and it's a good choice for me. 

#### HiKey
[WikiPage](https://github.com/96boards/documentation/wiki/HiKeyUEFI) - was built on official EDK2, and provides nothing but an UEFI env for booting any OS.

In addition, I can't find any information about external GPUs with HiKey boards or any achievements in getting PCIe lanes available.

PSS: really sad news, that this [repo and user](https://github.com/KNNSpeed/Simple-UEFI-Bootloader-ARM64) is down and any luck to have it restored.
PS: [lolz](https://community.nxp.com/t5/i-MX-Processors/i-MX8M-UEFI/td-p/754114)
