---
title: Password management with terminal love
layout: post-03242013
published: true
---

## Password management with terminal love

If you're at all interested in security and privacy, you probably have given
some thought to your passwords. Doing an internet search on the matter will
result in a lot of advice. For me it led to a semi-random approach to creating
passwords that were long and weren't easily guessed, but in my case it also
resulted in me having just a few passwords that I used and reused in most
places. One of them was actually the password for about 90% of my logins, so
even though it was pretty long (15 characters and not a dictionary word),
cracking it in one place was enough to get you access to 90% of my accounts.

Not particularly interested in some of the GUI solutions out there because I'm
more terminal oriented, I decided to search for my own solution. It's not very
hard and finding guidance on the matter was actually not hard either. For the
sake of covering it all in one place, I'm writing this post. Here is summary of
all the steps I took to get it done.

- install ccrypt for terminal based file encryptn and decryption
- set up a password directory and a password file
- create a shortcut for retrieving passwords from the terminal
- set up emacs to handle adding new passwords
- set up a password generator

### ccrypt for the terminal

ccrypt was the encryption solution that I found. It's a command line tool for
encrypting and decrypting files and even comes with a `ccat` utility to `cat`
the contents of an encrypted file. Most linux distributions have packages
available for it and I believe MacOS has it in homebrew. If not, I believe it's
simple to compile and doesn't have many dependencies. Do your distro a favor and
create the package/recipe, if it doesn't exist yet.

I use Arch linux, so the command for installing it was:

    # pacman -S ccrypt

### The Password File

After the install, you just have to create the file structure. I decided that I
wanted to store my password file in `~/.password/db.txt`

<pre>
$ mkdir ~/.password
$ touch ~/.password/db.txt
</pre>

There is nothing special about `db.txt`. You can name it whatever you like
because it's just a plain text file.

Let's see what your first password entries might look like:

<pre>
$ cat ~/.password/db.txt
example.com|flooose|abc123
notanexample.com|myemail`me.com|123abc
</pre>

So each password entry gets it's own line and each entry consists of three
fields separated by the `|` character. The field separator can be whatever you
like, but I like the pipe. Commas might be good option as well, but you see that
the domain name of the login is there, as well as the login name and the final
field is the password.

### Encryption and Some Practice with ccrypt

Now, let's secure this file so that snooping eyes can't read it.

<pre>
$ cd ~/.password
$ ccrypt db.txt
Enter encryption key:
</pre>

That's it, your `db.txt` has been replaced with `db.txt.cpt` and you've now got
your passwords stored securely in a file. Now let's retrieve them:

<pre>
$ ccat db.txt.cpt
Enter decryption key:
example.com|flooose|abc123
notanexample.com|myemail@me.com|123abc
</pre>

Decrypting the file is done like so:

<pre>
$ ccdecrypt db.txt.cpt
Enter decryption key:
</pre>

and now you have your `db.txt` back. These are all the tools needed for secure
password management, but it's still a pretty clunky way of doing it, so let's
make things more convenient.

### Easy Password Retrieval

So your password file will potentially get pretty big. Even terminal output of
twenty lines can be a strain on the eyes, and running `ccat` output through
`grep` every time is too much typing to help encourage good password practices,
so let's set up a
[bash function](/2012/03/23/from-aliases-to-functions-in-bash.html) that does
this for us.

Just copy the following lines into your `.bashrc` and any new terminal windows
will have the `p` funtion available to it (you can also do `$ source ~/.bashrc`
to get the function available in the current window):

<pre>
function p(){
    ccat ~/.password/db.txt.cpt | grep $1
}
</pre>

This function could be used as follows

<pre>
$ p notanexample.com
Enter decryption key:
notanexample.com|myemail@me.com|123abc
</pre>

There you go. Now your passwords are easy to retrieve.

### Easy password adding

Now let's deal with the pain of adding new passwords to our file. We don't want
to unencrypt the file in the terminal every time, just so that we can use our
editor to add a password, only to manually re-encrypt the file afterwards.

Fortunately the source for ccrypt comes with a great emacs plugin that makes it
work well with `.dbt` files (I'm sure one exists for vim too). The name of the
file is `ps-crypt.el` and if you simply copy it to some place in your emacs load
path and add the following lines to your `~/.emacs` file then new instances of
emacs should be able to decrypt and encrypt your `db.txt.cpt` file on the fly:

<pre>
;; ccrypt integration
(require 'ps-ccrypt "ps-ccrypt.el")
</pre>

Now you can do:

<pre>
$ emacs ~/.password/db.txt.cpt
</pre>

You can even streamline this a little more by writing another funtion in our
`.bashrc`:

<pre>
function padd(){
    emacs ~/.password/db.txt.cpt
}
</pre>

which will now allow you to replace

<pre>
$ emacs ~/.password/db.txt.cpt
</pre>

with

<pre>
$ padd
</pre>

### Password Generation

Now that you have the infrastructure for management of unique passwords on each
site, it's time to set up the infrastructure for generating strong unique
passwords. Again, this should be easily covered by most linux distros with the
`makepasswd` package. After installation, simply calling `makepasswd` will get
you a lovely string of characters and numbers for your password.

There are quite a few options, so it might be helpful to explore those for
yourself.

### Other considerations

For me this setup is mostly satisfactory, but I'm considering writing a function
for emacs that allows me to generate a password from within the editor. This
will allow me to avoid having to copy the password generated in the terminal
into emacs. If I get to it, I'll post it here.

Furthermore, I'm considering rewriting `padd` in such a way that I can use
Gnome's `alt-F2` feature to start emacs editing my db.txt.cpt file. At the
moment, I only know how to do this by rewriting the function as a stand alone
script and I want to avoid that if it's not necessary.

However, with these two changes, I wouldn't need the terminal at all for adding
passwords, which would be cool because then the terminal would only be used for
password retrieval.
