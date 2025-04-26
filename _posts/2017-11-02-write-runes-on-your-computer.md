---
layout: post
title:  "Write Runes on Your Computer"
date:   2017-11-01 23:27:57 -0700
categories: futhorc 
---
I wrote a post with this same title ages ago on my old blog, which I don't really feel like bringing back (something about being haunted by things I wrote 15 years ago). But I swear, this post is better anyway!

So in short, there are fonts out there that let you write runes or other ancient sets of characters, but they map the symbols to Roman characters. Meaning you type 'a' and you get ᚪ. You change to a different font, you get an a instead of an ᚪ. That's fine if you're able to force the text to use a certain font and only that font or if the final product is an image, not text.

Modern languages don't have to deal with these kinds of limitations usually. I type out a Japanese sentence like this 大阪へようこそ and I don't need to specify the font for it to be legible and recognizable as Japanese text. Your computer will render that with some Japanese font and that won't change the meaning of the text. The code points used here don't overlap with Roman characters. We could be on radically different computers, but you and I will see the same 大. Even emoji adhere to these rules more or less, allowing different types of phones and computers to see a kimono, laughing face or taco, albeit with subtle variations in style.

Believe it or not, you can do this with character sets for many extinct languages too! There are caveats because naturally, many man hours go into making sure widely spoken languages function well with modern systems, while little thought is put into making the user experience for language nerds, pagans and bewildered Vikings who accidentally stepped into a time warp as smooth as possible.

## Fonts

So this is specifically about the angular runes used in many Germanic languages before they switched over to using Roman characters (becoming "latin-based" as computer people like to say), but much of what I'm saying here applies to other ancient character sets. Runes are in the unicode standard yet most computer systems don't by default have fonts to cover that range. If the below paragraph looks like gibberish, then you need to install a font.

ᚳᚫᚾ᛫ᚷᚢ᛫ᛋᛁ᛫ᚦᛁᛋ

If you're on a modern desktop system that supports true type fonts (so Windows, Mac, Linux, others), then you simply need to install at least one font that covers the range. Here are two links that can help but you can also search for "unicode rune font":

* [Runes (ᚠᚢᚦᚨᚱᚲ) on the Web](http://www.personal.psu.edu/ejp10/blogs/gotunicode/charts/runes.html) - has links to several good rune fonts
* [Code2000](http://www.fontspace.com/james-kass/code2000) - font that set out to cover the whole unicode range, including runes

### Android 

I'm not familiar enough with the situation there beyond I'm pretty sure you won't be covered by default and need to install fonts (if that's possible?). Please someone who knows tell me because I'm curious.

### iOS

Miraculously, the latest iOS as of this writing (11) has runes and various other ancient scripts covered by default! iOS doesn't allow you to install fonts so if you aren't able to upgrade, then you're going to miss out on unicode runes 😢

## Keyboards

Not all sets of runes are equal. There was the original runic alphabet, Elder Futhark, used by older Old Norse. Futhark was expanded with additional symbols to make Futhorc, which was used by Old English and Old Frisian. There was also Younger Futhark, which actually had fewer runes than Elder Futhark. There were others as well, but these are some of the main ones you'll encounter.

Anyway, what you will need to configure on your system is a way to type runes and usually it will ~~not~~ be geared for one set of runes or another. I tend to focus on Futhorc because it's a broader set that I feel is more appropriate for modern English (just as it was for Old English).

### Windows

I feel [this page](http://www.babelstone.co.uk/Keyboards/Runic.html) has the best layouts for Windows and their futhorc keyboard is what I based the one I made for Mac OS X on. It's also just a good site overall for language nerds, with tons of good information and other language families.

[this keyboard](http://www.heathenhof.com/learn-old-norse/runic-keyboard-for-pc/) also looks pretty good.

### Mac OS X

I haven't found much made by others, so I made my own. [Check it out here](https://github.com/osakared/futhorc-keyboard-macosx), where there are also instructions for installing it. This is just Futhorc, I would like to ultimately make Mac clones of all the awesome ones BabelStone made.

### Linux

Just like with Mac OS X, I made my own, not having seen other options. Check out the [futhorc layout for linux](https://github.com/osakared/futhorc-keyboard-linux) I made. I have links to some hints on how to install.

### Android

If you know anything about this, please let me know.

### iOS

Inspired by Apple doing something I never thought they'd do and adding unprecedented coverage of unicode code points in their latest OS, I made a [Futhorc keyboard for iOS](https://itunes.apple.com/us/app/anglo-saxon-futhorc-keyboard/id1301122103?mt=8). If enough people download this, I'll add other features and other keyboard apps for other ancient languages.

## Orthography

Runes were made for writing various extinct Germanic languages (North and West). Of course modern languages, even the descendants of the old languages in question don't have the same set of phonemes. Shifts occurred over time. This is a subject for another post in another time, but there are a few basic principals that I recommend keeping in mind:

__Phonetic spelling__

Modern English is a mess. Why not make things easier and leave out silent letters? Futhorc has a perfectly good rune for making the th sound, ᚦ so there's no need to make something awkward like ᛏᚻ.

__But maybe not too phonetic__

English also has a lot of dialects so if you spell things phonetically to a T, then no two people will spell the same word the same way. It might be good to toss out some nuance or focus on the most neutral dialects. The t in tree is more of a ch sound but I think we can just pretend it's a t for the sake of rendering it in runes.

__Letters can change meaning__

Spelling phonetically doesn't have to mean according to the rules used in the original context. Both Dutch and Spanish spell things phonetically but the letters can have quite different meanings in the two languages. Futhorc has a symbol for a diphthong we don't have in Modern English, at least not in my dialect (ᛠ). Might be good to repurpose that for a common modern diphthong, like maybe the i in bike.

## Conclusion

Be a nerd. Have fun. Send secret messages to your friends in runes.