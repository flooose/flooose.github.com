---
title: Mounting encrypted geli partitions
layout: post-03242013.html
published: true
tags: [freebsd, geli]
---

## Mounting encrypted `geli` partitions

For people running FreeBSD with encrypted `geli` partitions on their
old laptop-turned-server, here's how you you can mount that partition
in, say, a FreeBSD live VM, when said laptop finally crashes.

From the live console:

    # geli attach /dev/ada0p3
    password:
    # mkdir /tmp/freebsd_partition
    # mount /dev/ada0p3 /tmp/freebsd_partition

That's it, and certainly not difficult. I posted it here though
because I didn't expect it to be this easy, I didn't find much in the
internet about it and because I won't remember this the next time I'm
faced with this problem.
