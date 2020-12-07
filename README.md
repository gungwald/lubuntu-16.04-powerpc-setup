# Lubuntu 16.04 on a PowerMac G4/533 (Digital Audio) - Model M5183

These instructions are specific to setting up Lubuntu 16.04 on 
PowerMac G4/533 (Digital Audio).
https://everymac.com/systems/apple/powermac_g4/specs/powermac_g4_533.html

This particular model has a Nvidia GeForce 2 MX
which is problematic for Linux.

# Stupid USB boot instructions

## First really stupid thing: Cold boot required!

You must do a cold boot. If you don't, at the end of it all,
it won't be able to find the boot partition 'cd:,\\:tbxi'

## Boot to Open Firmware

To boot into Open Firmware, hold down this key sequence during boot: 

    Option + Apple + O + F

## Help in Open Firmware command line

There is no help, so don't bother trying.

## Find USB drive

Find the location of your usb drive. It will be named disk@n,
on either usb@18 or usb@19:

    dev /
    ls

To rescan for an inserted usb drive:

    probe-usb

I think. Sometimes this hangs/crashes and you have to
reboot. So don't actually do that.

## Set an alias for the boot disk

Set an alias for booting. It must be 'cd' because farther
along the boot process expects it to be 'cd'. So you must
overwrite the existing 'cd' devalias.

    devalias cd /pci@f2000000/usb@18/disk@1

To view aliases type:

    devalias

## Extra-stupid boot command

    boot cd:,\\:tbxi

Yea, that's the boot command... Ponder that while you're meditating
on mushrooms.

If you did not do a cold boot, this command will fail. So, start
over from the beginning or go back to your PC and forget this
nonsense.

# Starting Xorg

## Kernel 4.x

The nouveau kernel driver does not work. The older nvidiafb kernel driver must be
used instead.

The kernel parameter "nomodeset" is required to turn off KMS because the 
Nvidia drivers and/or frame buffer drivers do not work with KMS.

    Comment out the "blacklist nvidiafb" line in /etc/modprobe.d/blacklist-framebuffer.conf.
    Add a line in /etc/modprobe.d/blacklist-framebuffer.conf to blacklist nouveau.
    Add these to kernel parameters in /etc/yaboot.conf: nomodeset video=offb:off
    Reinstall yaboot: sudo ybin -v
    Reboot

## Kernel 5.x

(Untested by me)

Linux Kernel 5.x does not work with X nouveau or fbdev so X will not run
at all under Kernel 5.x.

Olivier Laverré
With Debian (Linux Kernel 5.x), also add to your yaboot.conf:
append="video=TV-1:d nouveau.config=NvPowerChecks=0". This avoids a black screen. Or add it at the boot prompt: " Linux video=TV-1:d nouveau.config=NvPowerChecks=0".

OR, using this boot parameter:

    video=nvidiafb:1280x1024-16

# Partitioning

The install software will repartition the drive and then go on with
the install. But this never works. The machine has to be rebooted
after the repartition or nothing done after the repartition will
actually work, even though it seems to during the install. You
then have to redo the whole installation process using the same
exact partitioning scheme. I'm not sure what the best way to handle
this is. Possibly to abort the install and reboot right after
the new partition table is written to disk. Then restart the
install using the same partitioning scheme.

The install program should really force a reboot after partitioning
and then resume at the point right after the partitioning after the
reboot.

# Machine Info

/proc/cpuinfo
processor	: 0
cpu		: 7450, altivec supported
clock		: 733.333331MHz
revision	: 2.0 (pvr 8000 0200)
bogomips	: 66.58

total bogomips	: 66.58
timebase	: 33290001
platform	: PowerMac
model		: PowerMac3,4
machine		: PowerMac3,4
motherboard	: PowerMac3,4 MacRISC2 MacRISC Power Macintosh
detected as	: 69 (PowerMac G4 Silver)
pmac flags	: 00000010
L2 cache	: 256K unified
pmac-generation	: NewWorld
Memory		: 1024 MB


lspci:
0000:00:0b.0 Host bridge: Apple Inc. UniNorth 1.5 AGP
0000:00:10.0 VGA compatible controller: NVIDIA Corporation NV11 [GeForce2 MX/MX 400] (rev a1)
0001:10:0b.0 Host bridge: Apple Inc. UniNorth 1.5 PCI
0001:10:13.0 USB controller: VIA Technologies, Inc. VT82xx/62xx UHCI USB 1.1 Controller (rev 50)
0001:10:13.1 USB controller: VIA Technologies, Inc. VT82xx/62xx UHCI USB 1.1 Controller (rev 50)
0001:10:13.2 USB controller: VIA Technologies, Inc. USB 2.0 (rev 51)
0001:10:15.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL-8185 IEEE 802.11a/b/g Wireless LAN Controller (rev 20)
0001:10:17.0 Unassigned class [ff00]: Apple Inc. KeyLargo Mac I/O (rev 03)
0001:10:18.0 USB controller: Apple Inc. KeyLargo USB
0001:10:19.0 USB controller: Apple Inc. KeyLargo USB
0002:20:0b.0 Host bridge: Apple Inc. UniNorth 1.5 Internal PCI
0002:20:0e.0 FireWire (IEEE 1394): LSI Corporation FW322/323 [TrueFire] 1394a Controller
0002:20:0f.0 Ethernet controller: Apple Inc. UniNorth GMAC (Sun GEM) (rev 01)
