---
title: an olinuxino Utility Server hack
layout: post-03242013.html
published: true
tags: [arch-linux-arm, olinuxino]
---

## An olinuxino Utility Server hack

I needed a utility server to do some experiments and sync some data
between systems. After setting up
[system](https://www.olimex.com/Products/OLinuXino/A20/A20-OLinuXino-MICRO/open-source-hardware)
with [arch linux arm](https://archlinuxarm.org/) I realized that the
io on the sdcard was simply too slow for there server to function as a
dependable system, so I devised the following hack that boots system
from sdcard, but mounts the root file system from the attached
external hard drive resulting in a system whose io performs just like
that of a normal system.

Assuming you've finished the installation instructions [here](https://archlinuxarm.org/platforms/armv7/allwinner/a20-olinuxino-micro),

1. Boot the system and log in. I did it over a usb serial console
   using the cable available
   [hier](https://www.olimex.com/Products/Components/Cables/USB-Serial-Cable/USB-Serial-Cable-F/),
   but I imagine an `ssh` connection works as well. If you are using
   the serial console, here's the command I used to connect with it
   through `screen`

       # sudo screen /dev/ttyUSB0 115200

1. Now mount the sata drive to `/mnt`

       # mount /dev/sda /mnt

1. Excluding `/boot`, copy all other non-ephemeral directories to `/mnt`:

       # ls /
       bin  boot  dev  etc  home  lib  lost+found  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
       # cp -r /bin /etc /home /lib /opt /root /sbin /srv /usr /var /mnt

1. Create ephemeral directories on `/mnt`

       # mkdir /mnt/dev /mnt/mnt /mnt/proc /mnt/run /mnt/sys /mnt/tmp

1. Here's the actual hack. Edit `/boot/boot.scr`. This is actually a
   binary file created from a `boot.cmd` file, but I don't have this
   file and I don't understand all this `sunxi` stuff yet, so I tried
   editing the file directly, and it worked. The relevant text that
   needs to be changed is `root=PARTUUID=${uuid}` and needs to be
   replaced with `root=/dev/sda1`. This seems to tell the boot process
   that the root file system is on `/dev/sda1` instead of the same
   file system as the boot partition.

And that's it. Now when you reboot, you should be able to call `mount`
and get output similar to:

    # mount
    ...
    /dev/sda1 on / type ext4 (rw,relatime,data=ordered)
    ...

or you could do

    # df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/root       147G  2.3G  137G   2% /

and see that your root filesystem reflects a much larger size than
that sdcard that you were using when you started.

### Extra credit

If you like cleaning up unnecessary stuff, you can now shutdown the
system again, mount the sdcard on your normal computer and delete all
but the `/boot` folders on the sdcard.

After doing this, the total amount of space used on the sdcard was
reduced to just 27MB.

    $ du -hs ~/mnt/sdcard
    27M     /home/chris/mnt/sdcard

### Conclusion

With this in place I can use the system as a host to run multiple
servers in various containers on this olinxino host. Each server will
be in its own container. I don't have any high performance needs, so
this should be sufficient to run 3-4 services.
