---
title: Color Schemes in iTerm2
layout: post-03242013
published: true
---

## Color Schemes in iTerm2

After I recently switched to a new color scheme in my editor, I found myself
trying to recreate it in iTerm. After researching a bit, I discovered that it
wasn't that difficult. There is a
[site](http://code.google.com/p/iterm2/wiki/ColorGallery for user created color)
schemes and you can use any of the schemes there as a template for yours. I used
the [Solalized](http://ethanschoonover.com/solarized theme as my starting point):

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
	    <key>Ansi 0 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.19370138645172119</real>
		    <key>Green Component</key>
		    <real>0.15575926005840302</real>
		    <key>Red Component</key>
		    <real>0.0</real>
	    </dict>
	    <key>Ansi 1 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.14145714044570923</real>
		    <key>Green Component</key>
		    <real>0.10840655118227005</real>
		    <key>Red Component</key>
		    <real>0.81926977634429932</real>
	    </dict>
	    <key>Ansi 10 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.38298487663269043</real>
		    <key>Green Component</key>
		    <real>0.35665956139564514</real>
		    <key>Red Component</key>
		    <real>0.27671992778778076</real>
	    </dict>
	    <key>Ansi 11 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.43850564956665039</real>
		    <key>Green Component</key>
		    <real>0.40717673301696777</real>
		    <key>Red Component</key>
		    <real>0.32436618208885193</real>
	    </dict>
	    <key>Ansi 12 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.51685798168182373</real>
		    <key>Green Component</key>
		    <real>0.50962930917739868</real>
		    <key>Red Component</key>
		    <real>0.44058024883270264</real>
	    </dict>
	    <key>Ansi 13 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.72908437252044678</real>
		    <key>Green Component</key>
		    <real>0.33896297216415405</real>
		    <key>Red Component</key>
		    <real>0.34798634052276611</real>
	    </dict>
	    <key>Ansi 14 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.56363654136657715</real>
		    <key>Green Component</key>
		    <real>0.56485837697982788</real>
		    <key>Red Component</key>
		    <real>0.50599193572998047</real>
	    </dict>
	    <key>Ansi 15 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.86405980587005615</real>
		    <key>Green Component</key>
		    <real>0.95794391632080078</real>
		    <key>Red Component</key>
		    <real>0.98943418264389038</real>
	    </dict>
	    <key>Ansi 2 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.020208755508065224</real>
		    <key>Green Component</key>
		    <real>0.54115492105484009</real>
		    <key>Red Component</key>
		    <real>0.44977453351020813</real>
	    </dict>
	    <key>Ansi 3 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.023484811186790466</real>
		    <key>Green Component</key>
		    <real>0.46751424670219421</real>
		    <key>Red Component</key>
		    <real>0.64746475219726562</real>
	    </dict>
	    <key>Ansi 4 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.78231418132781982</real>
		    <key>Green Component</key>
		    <real>0.46265947818756104</real>
		    <key>Red Component</key>
		    <real>0.12754884362220764</real>
	    </dict>
	    <key>Ansi 5 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.43516635894775391</real>
		    <key>Green Component</key>
		    <real>0.10802463442087173</real>
		    <key>Red Component</key>
		    <real>0.77738940715789795</real>
	    </dict>
	    <key>Ansi 6 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.52502274513244629</real>
		    <key>Green Component</key>
		    <real>0.57082360982894897</real>
		    <key>Red Component</key>
		    <real>0.14679534733295441</real>
	    </dict>
	    <key>Ansi 7 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.79781103134155273</real>
		    <key>Green Component</key>
		    <real>0.89001238346099854</real>
		    <key>Red Component</key>
		    <real>0.91611063480377197</real>
	    </dict>
	    <key>Ansi 8 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.15170273184776306</real>
		    <key>Green Component</key>
		    <real>0.11783610284328461</real>
		    <key>Red Component</key>
		    <real>0.0</real>
	    </dict>
	    <key>Ansi 9 Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.073530435562133789</real>
		    <key>Green Component</key>
		    <real>0.21325300633907318</real>
		    <key>Red Component</key>
		    <real>0.74176257848739624</real>
	    </dict>
	    <key>Background Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.15170273184776306</real>
		    <key>Green Component</key>
		    <real>0.11783610284328461</real>
		    <key>Red Component</key>
		    <real>0.0</real>
	    </dict>
	    <key>Bold Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.56363654136657715</real>
		    <key>Green Component</key>
		    <real>0.56485837697982788</real>
		    <key>Red Component</key>
		    <real>0.50599193572998047</real>
	    </dict>
	    <key>Cursor Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.51685798168182373</real>
		    <key>Green Component</key>
		    <real>0.50962930917739868</real>
		    <key>Red Component</key>
		    <real>0.44058024883270264</real>
	    </dict>
	    <key>Cursor Text Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.19370138645172119</real>
		    <key>Green Component</key>
		    <real>0.15575926005840302</real>
		    <key>Red Component</key>
		    <real>0.0</real>
	    </dict>
	    <key>Foreground Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.51685798168182373</real>
		    <key>Green Component</key>
		    <real>0.50962930917739868</real>
		    <key>Red Component</key>
		    <real>0.44058024883270264</real>
	    </dict>
	    <key>Selected Text Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.56363654136657715</real>
		    <key>Green Component</key>
		    <real>0.56485837697982788</real>
		    <key>Red Component</key>
		    <real>0.50599193572998047</real>
	    </dict>
	    <key>Selection Color</key>
	    <dict>
		    <key>Blue Component</key>
		    <real>0.19370138645172119</real>
		    <key>Green Component</key>
		    <real>0.15575926005840302</real>
		    <key>Red Component</key>
		    <real>0.0</real>
	    </dict>
    </dict>
    </plist>

Looking at this XML there really isn't a lot of complexity. It's mostly contexts
of some sort and red, green and blue color definitions. What confused me though
were the values that were in the `<real>` tags. I thought that it might be some
way of specifying colors that I wasn't familiar with. I know RBG and html
colors, but I had never seen decimal numbers.

I can't remember where I found it, but the trick was that these floating point
numbers were actually percentages of the respective RGB values. So let's say you
have values of R=45, G=55 and B=67. You have to divide these numbers by 255 to
get the percentages (values) for our `<real>` tags. In my case, the RGB colors
45, 55, 67, were the values for the Background Color, so I put them in the Red,
Blue and Green Components of the `<key>Background Color</key>`.

Once you've done this, creating a color scheme for iTerm2 is quite trivial.

If you use emacs and use the Misterioso color theme, then you may want to check
out my [iTerm2
scheme](https://github.com/flooose/public_dots/blob/master/Misterioso.itermcolors.)
I haven't quite finished it. The background, foreground, cursor color and so on
are set, but you'll notice that in the above XML there are `<key>Ansi XX
Color</key>` tags. These are used for colored output in, for instance, the `ls`
command. Once I get those, I'll probably move the color theme to a separate
repository.

<p style="background-color:#2d3743">background-color #2d3743</p>

<p style="background-color:#e1e1e0">foreground-color #e1e1e0</p>

<p style="background-color:#415160">cursor background #415160</p>

<p style="background-color:#338f86">highlight background #338f86</p>

<p style="background-color:#e1e1e0">highlight foreground #e1e1e0</p>

<p style="background-color:#23d7d7">fontlock builtin #23d7d7</p>

<p style="background-color:#74af68">fontlock comment #74af68</p>

<p style="background-color:#008b8b">fontlock constant #008b8b</p>

<p style="background-color:#00ede1">function name bold #00ede1</p>

<p style="background-color:#ffad29">keyword #ffad29</p>

<p style="background-color:#e67128">string foreground #e67128</p>

<p style="background-color:#34cae2">typeface #34cae2</p>

<p style="background-color:#dbdb95">variable name #dbdb95</p>

<p style="background-color:#ff4242">warning #ff4242</p>
