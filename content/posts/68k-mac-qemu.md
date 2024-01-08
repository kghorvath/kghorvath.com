---
title: "68k Mac Emulation in QEMU"
date: 2024-01-07T23:29:55-05:00
draft: false
---
For a long time, the best options for emulating Old World Macs was either Basilisk II or Mini vMac. Now thanks to progress with the qemu-system-m68k emulator, it’s now possible to emulate a Quadra 800 in QEMU, with support for System 7 and Mac OS 8, as well as A/UX 3.0.

As of [QEMU 8.2.0](https://wiki.qemu.org/ChangeLog/8.2#68k), Quadra 800 support is now part of upstream builds, with support for running System 7.1 through Mac OS 8.1 and A/UX 3.0.1.

Starting off, you'll need to create two disk images, one PRAM image to hold firmware variables like resolution and startup disk, and your hard disk image.

`qemu-img create -f raw pram-q800.img 256b`

`qemu-img create -f qcow2 macintoshhd.qcow2 1G`

You'll also need a System Software installation CD for [System 7.1-7.6](https://winworldpc.com/product/mac-os-7/71) or [Mac OS 8.0-8.1](https://winworldpc.com/product/mac-os-8/85) and a Quadra 800 ROM dump (available if you look hard enough). Once you have everything, something similar to the following incantaction should get you going:

`qemu-system-m68k.exe -M q800 -m 128 -bios q800.rom-drive file=pramq800.img,format=raw,if=mtd -device scsi-hd,scsi-id=0,drive=hd0 -drive
file=macintoshhd.qcow2,media=disk,format=qcow2,if=none,id=hd0 -device
scsi-cd,scsi-id=3,drive=cd0 -drive
file=system761.iso,media=cdrom,if=none,id=cd0`

If everything goes well, you should hear the startup chime, see the Happy Mac, and soon be greeted with a Macintosh desktop.

![Mac OS 8.1 CD-ROM desktop](/images/68k_mac_1.png)

You’ll have to initialize the disk first before you can install Mac OS. Since we’re emulating a SCSI disk, we'll have to use Apple HD SC Setup to initialize and partition the disk. In my experience, automatic partitioning didn’t properly utilize the whole disk, so you may have to manually tweak the partition layout.

![Apple HD SC Setup](/images/68k_mac_2.png)

From there, go ahead and run the installer and reboot (don't forget to change your startup disk).

![New installation desktop](/images/68k_mac_3.png)

