---
layout: post
title:  "Fixing wrong charset in Japanese mp3 files"
date:   2020-06-06 08:44:22 -0700
categories: python id3 shift-jis
---
I had some mp3s I downloaded from a publisher's website, but they were all shift-jis encoded but marked as latin1. A common thing in Japan, unicode is still not at complete saturation. This script I completely whipped up is very single-purpose, one-shot. You'll likely have to modify it if you're having a similar problem.

```python
import os
import mutagen.id3

def findMP3s(path):
    for child in os.listdir(path):
        child= os.path.join(path, child)
        if os.path.isdir(child):
            for mp3 in findMP3s(child):
                yield mp3
        elif child.lower().endswith(u'.mp3'):
            yield child

for path in findMP3s('.'):
    id3= mutagen.id3.ID3(path)
    for key, value in id3.items():
        if value.encoding!=3:

            for i in range(len(value.text)):
                value.text[i]= value.text[i].encode('latin1').decode('shift-jis')

            value.encoding= 3
    id3.save()
```