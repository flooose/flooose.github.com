---
title: Setting up Allegro Components for Practical Semantic Web and Linked Data Applications
layout: post-03242013
published: true
---

## Setting up Allegro Components for Practical Semantic Web and Linked Data Applications

### Introduction

I've recently started working with [Mark Watson's](http://www.markwatson.com/)
[book](http://www.markwatson.com/opencontent_data/book_lisp.pdf) [Practical
Semantic Web and Linked Data
Applications](http://www.markwatson.com/books/) and found the setup of all
the [Allegro](http://franz.com/) components to be a major hurdle to getting
started. In fairness to Mark, he did say that it was beyond the scope of his
book to cover setting these things up, but considering the difficulty I
encountered, I feel like this topic needs more attention.

What follows is a step-by-step walk through of what it takes to get things set
up as painlessly as possible.

### Download the files

Before we can start, it's important to understand that we need three components
consisting of triple-store, a client library for querying this triple store, and
Allegro's own lisp implementation necessary for using this client library.

Because I did have some problems setting things up the first time, I recommend
using a "staging" folder to work from where the downloaded files are stored and
unpacked. On my system, I decided for this to be in `~/allegro/`. Furthermore, I
didn't install these components system wide, but instead in `~/lib/allegro/`

    $ mkdir ~/allegro
    $ mkdir -p ~/lib/allegro

Here are the links to the necessary files:

* [AllegroGraph](http://franz.com/ftp/pri/acl/ag/ag4.14/linuxamd64.64/agraph-4.14-linuxamd64.64.tar.gz) server used for triple-stores
* Allegro's Common [lisp](http://franz.com/ftp/pub/acl90express/linux86/acl90express-linux-x86.bz2) implementation
* The [client](http://franz.com/ftp/pri/acl/ag/ag4.14/linux86-acl90/agraph-4.14-linux86-acl90-client-lisp-acl9.0.tar.gz) libraries


### Install the server

This is the first step. Instructions can be found
[here](http://franz.com/agraph/support/documentation/4.14/server-installation.html#tarinstall)
and are actually pretty straight forward, but for me the commands basically
looked like this:

    $ tar zxf agraph-4.14-linuxamd64.64.tar.gz
    $ cd agraph-4.14
    $ ./install-agraph ~/lib/allegro/agraph-server-4.14

After asking a few configuration questions, the `install-graph` script will
install the server in `~/lib/allegro/agraph-server-4.14` and will output the
commands necessary for starting the server. I wrapped these in the following
script:

    $ cat ~/bin/agraph.bash
    #! /usr/bin/env bash

    /home/chris/lib/allegro/agraph-server-4.14/bin/agraph-control --config /home/chris/lib/allegro/agraph-server-4.14/lib/agraph.cfg $1

Assuming `~/bin/` is in your `PATH`, the server should now be able to be started
with:

    $ agraph.bash start

This can be varified by opening [localhost:10035](http://localhost:10035) in your
browser.

### Install and Update the Allegro Common Lisp

This is where I ran into the first bit of problems. I assumed I could use me own
clisp, but instead, Allegro's own implementation has to be used. The
instructions for the installation can be found
[here](http://franz.com/support/documentation/9.0/doc/installation.htm#express-inst-linfbsd)
and the command looked as follows

    $ cd ~/allegro
    $ bunzip2 < acl90express-linux-x86.bz2 | (cd ~/lib/allegro ; tar xf -)

This will install various components of Allegro's lisp in
`~/lib/allegro/acl90express` , but isn't sufficient for setting up the client.
Allegro has a concept of lisp "images", which apparently have certain features
en/disabled. Generating the necessary lisp image for working with the the
allegro client can be achieved by following the instructions in
`~/lib/allegro/acl90express/readme.txt` .

    $ cd ~/lib/allegro/acl90express
    $ ./alisp
    [1] CL-USER(1): (progn
        (build-lisp-image "sys:mlisp.dxl" :case-mode :case-sensitive-lower
                          :include-ide nil :restart-app-function nil)
        (when (probe-file "sys:mlisp") (delete-file "sys:mlisp"))
        (sys:copy-file "sys:alisp" "sys:mlisp"))

This will create a new executable `mlisp`, which should be used instead of the
`alisp` that was included in the zip-archive.

At this point, it's time to update the system, as mentioned in the
documentation:

    $ cd ~/lib/allegro/acl90express
    $ ./mlisp
    cl-user(1): (sys:update-allegro)

Finally, I also wrote a wrapper for starting `mlisp`. I did this with `rlwrap`
so that I had basic `readline` and history support:

    $ cat ~/bin/mlisp
    #! /usr/bin/env bash

    rlwrap -H ~/.alisp-history /home/chris/lib/allegro/acl90express/mlisp

### Install the client

Now we can install the client. This is the main difference from in the book,
where this is done from within an `mlisp`/`alisp` session. I'm not sure what I
did wrong, but in the book it's recommended to run

    (require :update)
    (system.update:install-allegrograph)

from within lisp, but I always got the error:


    Checking available AllegroGraph versions...
    Error: Builds of AllegroGraph for this platform are not currently being
           made available.  If you are interested in builds for this
           platform please contact support`franz.com and request them.
           Thank you.

I wasn't able to find a solution to this on the web, so here's an alternative
way to install the client software:


    $ cd ~/allegro
    $ tar -zxf agraph-4.14-linux86-acl90-client-lisp-acl9.0.tar.gz
    $ cp -r agraph-4.14-linux86-acl90-client-lisp-acl9.0 ~/lib/allegro/agraph-client-4.14

and following
the
[instructions](http://franz.com/agraph/support/documentation/v4/lisp-quickstart.html) for
testing the client:

    $ cd ~/lib/allegro/acl90express
    $ ./mlisp
    cl-user(1): :cd ~/lib/allegro/agraph-client-4.14
    cl-user(2): :ld agraph4.fasl
    cl-user(3): (in-package :db.agraph.user)

Because of the above error the `:cd` and `:ld` lines above are necessary any
time you see `(require :agraph)` in the book.

You should now also be able to create triple-stores with your configured client:

    ;; continued from the session above
    cl-user(4): (create-triple-store "ag-test" :user "chris" :password "abc123")

### Conclusion

You should now be able to jump into the various aspects of the book without any
setup problems. While this post is probably most relevant for people working
with the clisp version of this book, there might also be some parts relevant for
the multi-language version. I chose to start with the clisp version because I
didn't want to deal with the complexities of dealing with multiple languages.
I'm relatively comfortable with lisp and the languages in the
[multi-language](http://www.markwatson.com/opencontent_data/book_java.pdf)
version are all based on the JVM and at this point, I've been away from Java for
so long that I don't feel comfortable dealing with the overhead of deciphering
code examples in a foreign language (Java) while at the same time as learning
new technology.
