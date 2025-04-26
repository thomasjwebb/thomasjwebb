---
layout: post
title:  "Problem Updating Samba in FreeBSD"
date:   2018-02-21 21:39:24 -0800
categories: freebsd ports samba pidl
---
Usually updating FreeBSD versions is pretty easy for me, but I did run into a hiccup during `portmaster -a`:

```
Installing samba44-4.4.16_1...
pkg-static: samba44-4.4.16_1 conflicts with p5-Parse-Pidl44-4.4.16 (installs files into the same place).  Problematic file: /usr/local/bin/pidl
*** Error code 70
```

Apparently samba now comes with its own pidl due to a bug in the perl version of it. So deleting the package first fixed the issue:

```
pkg delete -f p5-Parse-Pidl p5-Parse-Pidl44
```