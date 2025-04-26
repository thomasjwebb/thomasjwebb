---
layout: post
title:  "My Bitwig and Git Setup"
date:   2018-08-17 21:23:57 -0800
categories: music git github gitlab
---
I spend most of my day on the weekdays working on code. I spend anywhere from zero to three hours on music, tending closer to the zero side of that range. So naturally my instincts when working on music are strongly influenced by how I work on code. I like to be organized. I like to keep things in repositories so I'm not hunting through old hard drives years later wondering where that one recording went. I also like to avoid exotic configurations that are hard to recreate, though this can sometimes come at the cost of spontaneity so I'll make exceptions to this from time to time at the latter stages of working on a song.

### Instruments

To avoid configuration annoyances if I need to setup Bitwig and my projects on a new computer, I try to avoid as many external dependencies as possible. I believe any good synth can make an infinite range of sounds and using too many different synths is a sign that you're not using your synths to their maximum potential. And if you use the built-in synths, that's one less thing you have to install. It's also hard to find synths that are on all three of Mac, Windows and Linux. I used to standardize on Korg Legacy Collection MS-20 as my one softsynth but getting that to work under Linux using wine was annoying. Now instead my one softsynth I use regularly is [obxd](https://obxd.wordpress.com/) which is open source and runs on everything Bitwig does.

I also keep hardware instruments to a minimum. Synths have infinite possibilities inside of them so no need to be a synth dilettante who just searches through presets. I get intimately familiar with synths and start with the init patch and work from there. For now, the only hardware synth I use regularly is my MS-20 mini. I'll talk more about hardware with future blog posts because there's something interesting - and analog - that I'm wiring up. But I like to keep hardware synths to a minimum because ultimately I'll have to bounce those tracks and that's audio I have to store. When it's just softsynths, all I'm storing is settings and midi. Nothing that should clog up a git repo.

### Sometimes You Do Need Audio

Okay but I can't avoid audio completely. I spice up my fm and virtual analog softsynths with some real analog. And sometimes there's vocals. And owing to my industrial influences, samples of things happening, maybe some not quite savory things, make their way into the mix. These, as I recall from the experience of keeping a game's code and assets in git isn't in keeping with the decentralized version control philosophy. So I use `git-lfs`. My `.gitattributes` file looks like this:

    *.wav filter=lfs diff=lfs merge=lfs -text
    *.aif filter=lfs diff=lfs merge=lfs -text
    *.multisample filter=lfs diff=lfs merge=lfs -text
    *.bwpreset filter=lfs diff=lfs merge=lfs -text

This includes the uncompressed audio formats used by the recordings made in bitwig, plus the `.multisample` files which have sample data embedded in them. The `.bwpreset`s are probably safe to version control normally but as it's closed-source software I don't fully know what's going on under the hood and I prefer to stay on the safe side with these files that are binary anyway. Who knows, maybe some of these presets do include audio data?

I also recently moved from github to gitlab to take advantage of free private repos. Importing won't get lfs so I manually changed the remote to the new gitlab one and pushed to it. However, it had a complaint I didn't quite understand about lfs:

`remote: GitLab: LFS objects are missing. Ensure LFS is properly set up or try a manual "git lfs push --all".`

Apparently I needed to have all the lfs objects on my system and *then* push them to the new origin. So setting old to be the github repo, I simply did this:

    git lfs fetch old --all
    git lfs push origin --all

And good to go!

### Directory Layout

I basically start by turning the `Bitwig Studio` folder that Bitwig creates (under Documents, My Documents, etc. depending on your os) into a git repo. That means I'm storing all the custom settings I make, my controller scripts and naturally the projects themselves. But I also keep lyrics and such under the same folder. Partly for historical reasons but why not have the ideas and the music itself side-by-side?

I simply have a lyrics directory filled with lyrics saved as markdown. I can easily edit them on gitlab (or github) and it creates good looking html from it as appropriate. I indent or otherwise mark as code any tabs, etc. I had a site a while back that enabled online collaboration on lyrics and some music collaboration features. Maybe the time to bring the idea back as an extension to github, et al is upon us? It would be cool to be able to click on guitar tabs and hear them played...