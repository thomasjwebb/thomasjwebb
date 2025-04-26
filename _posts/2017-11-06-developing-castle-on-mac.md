---
layout: post
title:  "Developing Castle on Mac"
date:   2017-11-06 10:18:58 -0700
categories: haxe castle gamedev 
---
The instructions in [castledb](https://github.com/ncannasse/castle)'s `README.md` don't cover Mac. I might suggest to `ncannasse` to add some but only after I'm sure the way I'm doing it is the best way to do it. Or, conversely, if this is now how it has to be done regardless of platform because of changes to `nwjs`. In short, just like for other platforms, I compile first:

    haxe castle.hxml

Then change to `./bin` and I directly run the executable. It doesn't matter if you copy it to that directory or just install it in `/Applications` and run from there but here I do the latter:

    ./nwjs/nwjs.app/Contents/MacOS/nwjs --load-extension . .

Also two of my minor fixes have been merged in as of this morning so now `ctrl+Q` actually quits, etc. Stay tuned for a bigger improvement that I need for a game I'm working on.
