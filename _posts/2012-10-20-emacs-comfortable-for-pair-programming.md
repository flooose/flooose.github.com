---
title: Emacs for pair programming
layout: post-03242013
published: true
---
## Emacs for pair programming

Somehow during my migration to emacs, I failed to realize that I never yearned
for a "Cmd-T" alternative. That changed when I started pair programming
regularly. People really count on it and actually, I understand why. I knew that
such a thing was possible in emacs and that solutions existed, but when it came
time to set it up (and in a way that people coming from non-emacs environments
expected it to function), it proved to be rather difficult. For the sake of the
intrepid emacs user who doesn't want to switch to a new environment, here's how
I finally got it set up right.

First off, get
[find-file-in-project.el](https://github.com/technomancy/find-file-in-project)
and put it somewhere in your emacs load path. If you want to make sure
compatibility hasn't changed,
[here's](https://github.com/flooose/_emacs/blob/master/modes/find-file-in-project.el)
the version that I'm using.

Now enter the following in your emacs config:

    ;; finding files
    (require 'find-file-in-project)
    (setq ffip-find-options' "-not -regex \".*vendor.*\" -not -regex \".*rsync_cache.*\"")
    (setq ffip-full-paths' t)
    (setq ffip-limit 1000)
    (setq ffip-patterns (concatenate 'list '("*.haml" "*.erb" "*.sass" "*.scss" "*.xml" "*.yml" "*.json") ffip-patterns))
    (global-set-key (kbd "C-c f") 'find-file-in-project)

I also have [ido](http://emacswiki.org/emacs/InteractivelyDoThings) and found
that the following setting from the previous link helped make the find results
more readable.

    ;; Display ido results vertically, rather than horizontally
    (setq ido-decorations (quote ("\n-> " "" "\n   " "\n   ..." "[" "]" " [No match]" " [Matched]" " [Not readable]" " [Too big]" " [Confirm]")))
    (defun ido-disable-line-trucation () (set (make-local-variable 'truncate-lines) nil))
    (add-hook 'ido-minibuffer-setup-hook 'ido-disable-line-trucation)

That's it. Now when you type `C-c f`, you'll get a nice little minibuffer at the
bottom of the screen that displays all of the search results.

For anyone interested, here's of a summary of what some of the lines above do.

First, the line

    ;; finding files
    (global-set-key (kbd "C-c f") 'find-file-in-project)

just sets the key binding. While vim users would type `C-p` and Textmate users
would type "Cmd-T", with my setup, you type `C-c f`

Next the line:

    ;; finding files
    (setq ffip-find-options' "-not -regex \".*vendor.*\" -not -regex \".*rsync_cache.*\"")

is mostly a performance issue. In my project, I end up with a `.rsynce_cache`
directory and I also install all of my gems in the `#{project_root}/}vendor`
directory, so I've customized this (as per recommendation in the
find-file-in-project source), not to search these directories because they don't
really interest me. This resulted in a significant performance boost.

At the same time the line

    ;; finding files
    (setq ffip-limit 1000)

sets the `find` limit to 1000 from the default 512. I did this because I noticed
that some results actually didn't show up because this limit wasn't set high
enough. Although 512 still seems like a high number for a normally sized
project, I guess fuzzy searching can include a lot of files.

Now the line:

    (setq ffip-full-paths' t)

just means, "show me the path to the file, not just the file name". This is good
if you're (for instance) editing chef files where there is no limit to the
number of `default.rb` files in your project.

Finally what I consider to be the most important part of the whole setup:

    ;; finding files
    (setq ffip-patterns (concatenate 'list '("*.haml" "*.erb" "*.sass" "*.scss" "*.xml" "*.yml" "*.json") ffip-patterns))

The default `find-file-in-project` setup doesn't include a lot of files in its
search that I use, so I've had to extend the default list.

The only other minor issue I've noticed with `find-file-in-project` is that it
gets problematic if you have nested git repositories. One of my projects has
this and if you're familiar with emacs' concept of `pwd`, then it isn't hard to
wrap your head around. Here's a short summary of the problem.

In emacs, `pwd` is always the directory of the file you're currently editing, so
editing `~/.emacs.d/somefile` followed by `~/some_other_file` means that you've
implicitly also changed directories from `~/.emacs.d` to simply `~/`. How this
ties into `find-file-in-project` is that when you invoke `find-file-in-project`,
it actually looks at `pwd` of the file you're currently editing and if it
doesn't find a `.git` file, it searches `pwd` 's parent directory. It continues
this until it finds a `.git` file.

So when you use `find-file-in-project` to find a file that happens to reside in
said nested git repository emacs changes to that file's directory, i.e a
subdirectory of a different git repository and when you then invoke
`find-file-in-project` again, this time from this file's directory,
`find-file-in-project` will interpret the nested git repository's directory as
the starting point for its search, resulting in files that you were expecting in
your search result not to show up.

There are a number of solutions to this. `find-file-in-project` comes with a
variable `ffip-project-file`. By default this variable is set to `.git`, so any
directory containing a `.git` file is considered a project-root and setting it
to any file not common to both your project-root and it's nested git repository
would solve the problem. In my case, my nested git repository doesn't have a
TAGS file and also no .gitignore file, so either of these would suffice to
differentiate my project-root from my nested git repository.

Ideally you would write a function that sets this variable when invoke emacs
from the command line, and define an additional keybinding for setting/updating
the variable while you're still in emacs. I'm lazy though, so I haven't
implemented any solution. When I do, I'll post it here.
