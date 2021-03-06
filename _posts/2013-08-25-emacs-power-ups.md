---
title: Emacs power-ups
layout: post-03242013
published: true
---

## Emacs power-ups

In the last weeks I've been fortunate enough to go through an emacs learning
spurt. On the one hand I had the fortune to solve some older problems that I've
just ignored or for which I had a work around and on the other hand, I've had
the fortune of running into problems that I've found solutions for right away.

For people who aren't quite the emacs veterans, I felt the following summary
would be helpful productivity boosters.

### Joining lines

I know emacs can join lines, but can it do it like vim? I work with vim users
and I really like "J"-functionality in normal mode and that's not how emacs does
it by default. A little extra research yielded a satisfying solution for
"J"-like functionality. Here it is, taken directly from
[emacsredux.com](http://emacsredux.com/blog/2013/05/30/joining-lines/) :

    (defun top-join-line ()
    "Join the current line with the line beneath it."
    (interactive)
      (delete-indentation 1))
    (global-set-key (kbd "C-^") 'top-join-line)

Summary: Go to line, press `C-^` and the line below will be joined to this one.

### Repeating commands

The original motivation for finding this solution was the "." command in vim
normal mode. It repeats your last change and it makes a handful of small
repetitive changes a breeze. Emacs doesn't really have this because in most
cases find and replace is synonymous to the dot command. What emacs offers in
addition to this is the ability to repeat complex commands with `C-x M-:` and
while it's not as convenient as a simple dot, it's also much more powerful
because you have the history of all your complex commands with their arguments
and you can edit them too!

### Finding files for other window

Whether you use ido or not, you have some sort of `find-file` function in emacs.
As a supplement to that, you have `find-file-other-window` function that opens
the file in a new emacs window. The problem is when you use a plugin that uses
fuzzy finding like
[find-file-in-project](https://github.com/technomancy/find-file-in-project) or
[projectile](https://github.com/bbatsov/projectile) that make finding files
easier, but don't deliver the "-other-window" functionality as well. This is
where you can take advantage of the fact that emacs is really just a great API.
I use Projectile and here is the implementation of `projectile-find-file`

    (defun projectile-find-file (arg)
      "Jump to a project's file using completion.
    With a prefix ARG invalidates the cache first."
      (interactive "P")
      (when arg
        (projectile-invalidate-cache nil))
      (let ((file (projectile-completing-read "Find file: "
                                              (projectile-current-project-files))))
        (find-file (expand-file-name file (projectile-project-root)))
        (run-hooks 'projectile-find-file-hook)))

Here you see that the actual finding of the file is done with emacs internal
`find-file` function. So here's my monkey patch plus key-binding code for using
Projectile to open files in other windows:

    (defun projectile-find-file-other-window (arg)
      "Jump to a project's file in other window.
    With a prefix ARG invalidates the cache first."
      (interactive "P")
      (when arg
        (projectile-invalidate-cache nil))
      (let ((file (projectile-completing-read "Find other window file: "
                                              (projectile-current-project-files))))
        (find-file-other-window (expand-file-name file (projectile-project-root)))
        (run-hooks 'projectile-find-file-hook)))
    (global-set-key (kbd "C-c f")  'projectile-find-file)
    (global-set-key (kbd "C-c 4 f")  'projectile-find-file-other-window)

### Delete to...

One things that vim enables for you in it's separation of modes is a wealth of
complex commands without having to introduce alt-, meta-, shift-, ctrl-
combinations. As emacs users, we know jokes that are made about us. One of the
vim features that I didn't miss for a long time was the delete-to functionality:
dt-someletter, df-someletter, dblah...-someletter-in-between-a-bunch-of-stuff...
There are a lot of powerful variations and meta-d, meta-d...meta-backspace feels
more natural most of the time, but sometimes, the vim functionality is just
nice. For a lot of those cases, there is `M-z` or `zap-to-char`

### follow-mode

This one is great. I discovered it by accident, but it's really helpful when
you're spiking a new feature or idea and you're just throwing a bunch of
prototype code into one file. It doesn't take long before the file gets too long
for the 40 to 50 lines of screen space you have and you suddenly lose focus on
your work because you're jumping back and forth between the code that you're
working on and the code that's out of view. For that, you can do `C-x 3` (open
another window), now if you do `M-x follow-mode`, the lines below the visible
parts of your first window will be displayed in the second window. It's like
reading a book, when you're reading the page on the left, you can always look at
the stuff coming on the page on the right. It helps!

### Browsing with a Sandwich

Some times you're reading code, or reviewing what you've coded. I always seem to
do this with an apple or a sandwich in my hand and this is where all the `C-n`s
and `C-p`s get difficult. Emacs really requires both hands on the keyboard, that
is, unless you know about view-mode. After doing `M-x view-mode`, `C-h m` gives
you a run-down of all the navigation possibilities. Whole page scrolling, half
page scrolling, it's all there. One customization I decided for right away
though, was the j, k functionality of vim. When I'm reading code, I'm lazy and I
usually do just scroll one line at a time, so this customization made sense to
me. Here's the code:

   ;; vi-like browsing in view mode
   (add-hook 'view-mode-hook
             (lambda()
               (local-set-key (kbd "j") 'View-scroll-line-forward)
               (local-set-key (kbd "k") 'View-scroll-line-backward)))

### Ispell

Most linuxen have some sort of concept of dictionaries and spelling. I won't go
into setting that up here, but assuming one of these programs is installed in
your system, `M-ispell` interactively goes through your text and suggests
spelling corrections when it finds an error. There is even a concept of a
personal dictionary in which your own words may be saved. A good example in this
paragraph was "linuxen". Now you've got another dot-file to manage :)

### Conclusion

Well, that's it. I hope someone gets some use out of this.
