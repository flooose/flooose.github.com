---
title: arch linux install with UEFI booting
layout: post-03242013.html
published: true
tags: [emacs, ansi-term, ps1, ssh]
---

## Introduction

Since I don't reinstall my system very often, I've neglected some (not so) new
developments in the booting process of computers. I've continued to rely on the
old MBR-style of booting, while everyone else was switching to UEFI-booting.
While installing [arch linux][0] in my new work computer, I decided to learn
what's behind UEFI-booting. The following is how I got it working.

## Prerequisites

I mainly used the arch linux guides, the [beginners guide][1], the [lvm][2] and
[luks][3] guide, the [UEFI-guide][5] and the [grub][4] guide. In addition I
found this [gist][6] a succinct summary of all steps involved. I didn't follow
the steps one-for-one, but they it did serve as an anchor point.

## Bootrapping arch

Bootstrapping arch linux with `lvm` and `luks` is pretty straight forward. You
partition the drives, run `cryptsetup` for hard drive encryption, open and mount
the crypted volume, use `pacstrap` and `arch-chroot` to get a bootstrapped
install, then from the `chroot`, you setup `lvm`. I use a simple `lvm` setup
with on root volume and one swap volume.

The partitioning scheme:

```
/dev/sda1 => uefi partition
/dev/sda2 => grub partition
/dev/sda3 => encrypted root
```

## The Boot Loader

Here I ended up with some problems with the boot loader. After a lot of trying
things out, I started to suspect, that there was something wrong with the UEFI
setup. Did I need an arch install image that had this enabled? Did I miss
anything in the documentation? I wasn't sure, but all I saw after `grub-install`
and a reboot, was that there was no sign of any sort of bootable hard drive.

The magic moment came when I noticed, there was nothing in `/sys/firmware/efi`.
This came after seeing that `efivar -l` didn't return anything. Looking at the
bios, I noticed that there was a setting for UEFI that seemed to support legacy
booting as well as UEFI-booting, or both. By setting the bios to use exclusively
UEFI, I was able to boot into the arch installer and see that
`/sys/firmware/efi` was there and that `efivar -l` now worked.

I guess the computer booted differently and the arch system loaded different
kernel modules than in the previous attempts. This was the important part to
getting the system bootable.

After that I was able to set the following mounts from the install system, run `arch-chroot` and run `grub-install` as described in the above mention gist

```
mount sda3 /mnt
mkdir /mnt/boot
mount /sda2 /mnt/boot/
mkdir /mnt/boot/efi
mount /sda1 /mnt/boot/efi
```

[0]: https://archlinux.org
[1]: https://wiki.archlinux.org/index.php/Beginners%27_guide "arch linux beginners' guide"
[2]: https://wiki.archlinux.org/index.php/LVM
[3]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS
[4]: https://wiki.archlinux.org/index.php/GRUB
[5]: https://wiki.archlinux.org/index.php/EFI_System_Partition
[6]: https://gist.github.com/mattiaslundberg/8620837
