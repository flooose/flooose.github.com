---
title: From Aliases to Functions. An introduction to bash Functions.
layout: post-03242013
published: true
---

## From aliases to functions

I'm writing this post because I've recently found myself writing a function in
bash and ran into some small problems that might make some reluctant bash users
give up and come up with some other solution, like a custom wrapper script
written in some language with which they are more comfortable and which they
then store in `~/bin`. This in essence is fine, but when bash is perfectly
capable of doing this, why not use bash.

I say reluctant because I know a lot of programmers who work in their terminals
every day, but only because it is a "necessary evil". I'm quite novice at bash
myself. I've done a fair amount of customizing of command line, custom key
bindings and such, but never spent any amount of time really programming in it.
The difference between the other programmers in my circles and I is really just
that I, unlike they, am not a reluctant, but rather a very interested bash user.

So, if you're still here, let's establish the motivation for writing a function
in bash. Here's a very simple use case:

I'm sure even most people who don't have experience writing functions in bash
have some aliases. The typical aliases I've seen are, for example, the most
common git commands, perhaps: git checkout => git co, git branch => git b, or
git commit => git c

The entries in your .bashrc would then look as follows:

<pre>
$ cat ~/.bashrc | grep alias
alias gitco='git checkout'
alias gitc='git commit'
alias gitb='git branch'
$
</pre>

Okay fine, but where do functions come in? Well, what happens if you want to
alias to a command that runs longer. For instance, you've installed program from
homebrew and Mac's "spotlight" (`cmd-space`) doesn't find it. One options is to
start it at the terminal, where you have control of the `PATH` variable.

Great. Then since it runs a long time, if you wanted to get it out of the way,
you could alias the command like so: `alias mycommand='mycommand &'`, which
would get the command to run in the back ground every time you use it.

There is still a problem though. What if you have to pass arguments to the
command? The above alias won't work. I haven't been able to figure out why the
`&` at the end of the alias makes it not be able to take arguments, but I've
found that an alias to any command that will be run in the background will not
take arguments.

So there it is, there's our motivation. In my specific case, I wanted to use
homebrew's `emacs`, and I was trying to write an alias along the lines of `alias
em='emacs &'`, but I was easily able to recreate it with `mvim`, and you could
choose any other long running program that doesn't daemonize. Here is a rather
contrived `mvim` example:

<pre>
$ cat ~/.bashrc | grep mvim
alias mvim='mvim -f &'
$
</pre>

Here we're overwriting the mvim command to call `mvim -f &`. The `-f` is telling
mvim to run in the foreground because it by default runs in the background, and
we need something running in the foreground for this example (again, we had to
contrive the example :).

So after setting this alias, running the command with an argument results in:

<pre>
$ mvim Gemfile
[2] 11028
-bash: Gemfile: command not found
$
</pre>

### Function Fundamentals

So, yeah, use case for functions. Googling for an answer to this will result in
the bash masters recommending functions as well. So let's write one. In bash
it's dead simple. We'll start by writing one right on the command line:

<pre>
$ function blah(){
> echo hah
> }
</pre>

We can also write it in one line like this:

<pre>
$ function blah(){ echo 'hah'; }
</pre>

If you do it like this, it's important to know, that the `;` before the `}` is
NOT optional and that the space before `echo` and after `;` are also NOT
optional.

Finally, if you prefer, the `function` keyword is also optional. So your one
line function could actually look like:

<pre>
blah(){ echo hah; }
</pre>

We can now execute the function the same way we'd call any other command:

<pre>
$ blah
hah
$
</pre>

### Making it useful

I guess we want our command to do something though. Let's do the (almost)
simplest thing possible and just try running our `emacs` command from it. If
you're not an emacs user, but want to stick to my example, there is just one
emacs command you need to know: `C-x C-c` or `^x ^c`, depending on which type of
notation you're used to. This command exits emacs and since we'll be starting it
a lot, you'll have to exit it a lot.

<pre>
$ cat blah.bash
function blah(){
    emacs
}
$
</pre>

As you can see, I'm editing the function in a file now. I'm doing this because
in the next step, we're going to have a little more code and I find it easier to
edit in a text editor. Let's run what we've got first though:

<pre>
$ source blah.bash
$ blah
Warning: arch-dependent data dir (/private/tmp/homebrew-emacs-23.3b-wol7/emacs-23.3/nextstep/Emacs.app/Contents/MacOS/libexec/emacs/23.3/x86_64-apple-darwin10.8.0/) does not exist.
</pre>

It works, although not really in a desirable way. Press `C-c` to kill the
process. In case you don't know it, the `source blah.bash` line above is how we
load the contents of that file into our environment.

Now, let's get this running a bit better:

<pre>
$ cat blah.bash
function blah(){
    EMACS='/Users/chris/.homebrew/Cellar/emacs/23.3b/Emacs.app/Contents/MacOS/Emacs'
    $EMACS &
}
$
</pre>

This time I've set an `EMACS` variable and invoked it with `$EMACS`. Since we've
got the `&` at the end of that line, we know it will run in the background
again. So let's `source` our file again and run the command:

<pre>
$ source blah.bash
$ blah
[1] 72929
$
</pre>

Wow, that was a great next step. Now we can start emacs with our function and it
doesn't lock up our terminal. I ran into a couple of difficulties at this step
that I think are worth pointing out. First I wasn't sure how to run the actual
command. Historically I've only used variables to store data, so it didn't occur
to me that a variable could be executed as well. With this in mind I was
thinking in terms of psuedo code like `exec "$EMACS"` or some similar
variations. It took a bit of time and some googling before I finally thought to
try the very simple `$EMACS`.

The second problem that I ran into was that I didn't know how to use the `&`
after the command. In bash you can optionally put a `;` after a line of code and
when I started, I was putting a `;` at the end of each line, which looked
something like `$EMACS &;` and resulted in errors. I'll leave it up to the
reader to find out when lines of code should have `;` after them and when not.

So, with that out of the way, let's get our function to do something with
arguments passed to it. Currently if we do `blah somefile.rb`, the editor gets
started, but it won't open the file.

Arguments to functions were also sort of counter intuitive for me. I didn't see
anyone doing, for instance:

<pre>
function blah(arg1, arg2) {
    # all my code
}
</pre>

Not wanting to get too distracted from the task at hand, I decided to go on the
hunt for some sort of "splat" operator that just takes all the arguments to
function and hands them off to the command. It turns out this operator is simply
`$*`. Here is the updated and final version of our function:

<pre>
$ cat blah.bash
function blah(){
    EMACS='/Users/chris/.homebrew/Cellar/emacs/23.3b/Emacs.app/Contents/MacOS/Emacs'
    $EMACS $* &
}
$
</pre>

Again, if you don't know it, there are many special parameters in bash. If
you're interested, just do `man bash` and search for "Special Parameters"

That's it. If we reload our source file once more, we can use our function as
that smarter alias we needed that would run in the back ground AND accept
arguments.

<pre>
$ source blah.bash
$ blah somefile
[1] 72929
$
</pre>

I hope this helps someone or makes them less reluctant to script a little
something in bash. I also if other language, say Ruby (in my case), could have
written this more concisely.
