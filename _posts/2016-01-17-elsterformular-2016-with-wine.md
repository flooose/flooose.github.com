---
title: Getting ElsterFormular 2016 Running with Wine on linux
layout: post-03242013.html
published: true
tags: [elsterformular, elsterformular-2016, 2016, ericliste, ericprozess, ericprozess.exe, wine]
---

## Getting ElsterFormular 2016 Running with Wine on linux

I've been doing my taxes here in Germany for the past few years with
the help of `wine` on linux. This year after installing
`ElsterFormular-17.0.4.20160106p.exe` from the
[Elster website](https://www.elster.de) I was presented with a handful
of error messages. Depending on where I was in the application I was
either getting something along the lines of `...Eric-Liste nicht
vorhanden...`, or `...ericprozess.exe unerwartet beendet...`

Here's how I fixed it. I simply started by finding the
`ericprozess.exe` file in my `wine` install and ran the program from
the command-line:

    $ $ find ~  -name ericprozess.exe
    /home/chris/.wine/drive_c/Program Files (x86)/ElsterFormular/bin/ericprozess.exe
    $ wine '/home/chris/.wine/drive_c/Program Files (x86)/ElsterFormular/bin/ericprozess.exe'
    err:module:load_builtin_dll failed to load .so lib for builtin L"WLDAP32.dll": libldap_r-2.4.so.2: cannot open shared object file: No such file or directory
    err:module:import_dll Loading library WLDAP32.dll (which is needed by L"C:\\Program Files (x86)\\ElsterFormular\\bin\\erictransfer.dll") failed (error c000007a).
    err:module:import_dll Library erictransfer.dll (which is needed by L"C:\\Program Files (x86)\\ElsterFormular\\bin\\ericctrl2.dll") not found
    err:module:import_dll Library ericctrl2.dll (which is needed by L"C:\\Program Files (x86)\\ElsterFormular\\bin\\ericapi.dll") not found
    err:module:import_dll Library ericapi.dll (which is needed by L"C:\\Program Files (x86)\\ElsterFormular\\bin\\ericprozess.exe") not found
    err:module:LdrInitializeThunk Main exe initialization for L"C:\\Program Files (x86)\\ElsterFormular\\bin\\ericprozess.exe" failed, status c0000135

From the error message you can see that `ericprozess.exe` expects some
sort of `ldap` library, namely `libldap_r-2.4.so.2`. I'm on
[arch linux](https://www.archlinux.org/) and after installing `wine`,
I got a message about optional dependencies:

    Optional dependencies for wine
    ...
    libldap [installed]
    lib32-libldap
    ...

You can see that `libldap` is installed, but `lib32-libldap` is
not. Simply installing `lib32-libldap` fixed the problem. If you're on
another linux, like ubuntu, something like
[`apt-get install libldap-2.4-2:i386`](http://askubuntu.com/questions/29665/how-do-i-apt-get-a-32-bit-package-on-a-64-bit-installation)
