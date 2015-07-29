---
layout: post
title:  "Keyboard shortcuts"
date:   2015-07-21 21:22:49
comments: true
author: Přemysl Donát
---
# Define keyboard shortcuts

Defining keyboard shortutcs in linux can be tricky. At least for me.

## Xbindkeys

1. Install 'xbindkeys'. For me it's `pacman -S xbindkeys`
2. Create sample configuration file with `xbindkeys -d > ~/.xbindkeysrc`
3. Inspect it. The syntax is really simple. It consist of two line units

Examples of volume control:

~~~
"amixer set Master toggle"
  m:0x0 + c:121

"amixer set Master 5%+"
  m:0x0 + c:123

"amixer set Master 5%-"
  m:0x0 + c:122
~~~

To-be-called **command is on first line**. Invoking **key combination on second line**.

To get the right keycodes(the weird `m:0x0 + c:121`) use `xbindkeys -k` from terminal.

White window pops up and when you press some key combination proper codes are printed to terminal.

You can either use cryptic codes like me or nice readable ones. The nice readable ones weren't working for me.

Shortcuts will work after reboot.

## If you have lxde and openbox(like there is better windows manager right..)

Then you can define keyboard shortcuts in openbox config.

1. Open `~/.config/openbox/lxde-rc.xml`
2. Grep for 'Keyboard'. There are multiple types of shortcuts
3. It's relatively easy to grasp
4. Most easy way is to copy existing commands and substitue your actions and shortcuts
5. Again they should be active after reboot

Well actually openbox got a good documentation where you can read it all:

[http://openbox.org/wiki/Help:Bindings](http://openbox.org/wiki/Help:Bindings)

and

[http://openbox.org/wiki/Help:Actions](http://openbox.org/wiki/Help:Actions)
