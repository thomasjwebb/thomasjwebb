---
layout: post
title:  "Strengths and Weaknesses of Using Haxe for Audio Development"
date:   2019-05-10 09:33:35 -0700
categories: haxe cpp c++ extern binding function pointer
---
At the [Haxe Summit](https://summit.haxe.org/us/2019/), I did a talk about doing audio programming in haxe. [Here are the slides](/assets/grig_presentation.pdf). Edit: [here is a link to the video](https://www.youtube.com/watch?v=IQs2a2KHlpk) in case you feel like watching me spill water. And below are elaborated notes for the first two slides.

## Advantages/Potential for Haxe as an Audio Programming Language

### Multi-target, multi-environment

You can get back more return on your time investment by using a versatile language. Is the synth you just made relegated to desktop apps but not the browser or other way around? Whichever way the tech goes, you want to be able to keep your options open with regard to dynamic languages vs. deploying compiled code on dynamic's turf (emscripten, webassembly).

### Targets multiple languages with vibrant audio communities

C++ and C obviously are popular languages for audio development and while haxe's generated C or C++ is usually not well-suited for using directly from the target language, you can make externs for already existing code in these languages and make use of those in haxe for the cpp and hl targets.

### Macros allow for compile-time optimizations and checks

Haxe's macros are excellent, and one use case is preventing certain operations within a given function. So you can ensure that your function doesn't have any calls to new, for example. Note that this is a high-level optimization and won't guarantee that mallocs or frees aren't happening somewhere under the hood (haxe can't stop the js interpreter from deciding to garbage collect at just the wrong time).

### Existing community of creatives, game programmers

Music/audio development and game development both attract creative types and there's considerable overlap in the people and the use cases. An existing game development community could be a boon to a burgeoning haxe audio community.

### Type system allows for generic algorithms

Like C++, haxe allows template parameters so that the same function can work with different types. Useful when you consider how often you want to write audio code that you want to work on different audio formats. Where type parameters are insufficient, macros can come in to generate code that handles specific types in ways even type parameters can't.

### Existing functionality for high-level operations such as playing audio files and game audio

For game applications, we already have a great deal of functionality already there - bindings or externs for openal and ability to load some audio formats for some targets/environments. OpenAL isn't commonly used in pro audio applications, but it's actually pretty cool that you have a partial implementation of the spatialization interface even when targetting the browser.

## Disadvantages

## No MIDI I/O (until now)

Haxe has a long history of not providing support for talking to midi ports. There is one lib I found that was ported from AS3 and it only talked to a socket, which in turn would be fed from an external non-haxe application. Now thanks to `grig.midi`, we do have midi port support in haxe for cpp, js/webmidi, js/nodejs and python targets. Also recent versions of haxe now include externs for webmidi, which `grig.midi` makes use of (previously, I had my own externs made for it).

### No pro audio-appropriate Audio I/O (until now)

The audio i/o functionality in lime, openfl and heaps is designed with games in mind and they are mostly based on OpenAL or a low-level interface with OpenAL abstraction on top (in the case of heaps). Functionality such as timing information in the callback, ability to query sound cards and capabilities, and setting bitrates are generally non-existent or hard to find. One partial exception is we now have externs for webaudio on the js target.

### Garbage collected language, even when targeting C++

Garbage collection can be tricky to deal with when doing audio programming. If something is allocating without your knowledge in code that's called over a hundred times a second (e.g., 48k with 256 buffers = 187.5x/second) then you can have performance issues. There are some workarounds, as always you have to be careful when making audio code in gc languages and it's not haxe's fault that you can't make any guarantees about what's happening across multiple different gc targets. The hl target provdes some flexibility that may be useful (the ability to specify your own gc) and haxe macros can be made to check for any high-level mistakes such as calling new in a callback. The cpp target also allows some tweaks to how gc is done.

Embedded development is one area where haxe is weak due to the gc. In that space, memory management can become very important. I'm not talking about throwing some apps on a raspberri pi, but programming for much lower power devices, something a larger company with larger sales where the economics favors paying more for engineers in order to pay less per unit. Tests should be done to see how far the compiled targets can be pushed. But the paucity of people using haxe this way means, just as with a lot of the audio stuff, that you're on your own for now.

### Fragmented compiled targets (hxcpp vs hashlink)

This isn't an issue when making code that should work with either, but adds extra work when providing technology that requires native externs, such as the audio and midi i/o I've worked on, which thus far is only present for hxcpp.

### No equivalent to `std::numeric_limits<Type>`

Sounds like a minor gripe, but incredibly annoying when trying to make generic algs that can work on multiple integer types of audio or converters (between integer types or between integer and float types). In C++, it's fairly easy to write templated functions that can work on multiple int types:

```c++
template <class T>
std::vector<float> convertToFloat(std::vector<T> in)
{
        std::vector<float> out(in.size());
        float min = std::numeric_limits<T>::min();
        float max = std::numeric_limits<T>::max();
        float range = max - min;
        for (size_t i = 0; i < in.size(); ++i) {
                out[i] = ((float)in[i] - min) / range;
        }
        return out;
}
```

This might not be feasible on all targets if the underlying representation of the numeric type can vary with interpreter, but should at least be provided where it can be.

### Some targets ill-suited for use from target language.

The only target I personally commonly use that does well in this respect is js. I have a feeling C# may also be in this elite category as of recently. However, two languages I use where it's not the case are python and C++. Python should be easily fixable and there are some okay workarounds, but the basic problem there is due to its history starting out as a hook into `Compiler.setCustomJSGenerator()` where it stuffs everything into one file. So haxe namespaces don't translate to python namespaces. This might be acceptable in internal projects, but I would never ship something that turns haxe dots into python underscores to `pip`. I can manually create wrappers so this is frustrating but minor.

Hxcpp and hashlink are tricker to deal with. C++ is a popular and very important language for audio programming so the ability to make c++ code that c++ coders can use would be very handy. Unfortunately the code that is produced is necessarily different to accomodate haxe's type system and garbage collection and hence somewhat difficult to interoperate with existing c++ code. Also see earlier complaints about garbage collection. There are of course workarounds such as a thin interface, perhaps exported c functions that are called from the other side. We might be stuck with this situation due to the design of haxe although streamlining the one workaround - c interfaces - might help.

### Few libs for machine learning or DSP

Audio code tends to involve DSP and at present, utility functions such as ffts, resamplers, etc. Haxe doesn't have an extensive selection of these things, so at present an audio developer would find themself having to reinvent the wheel in ways they wouldn't in c++, python or, increasingly, js. A new frontier in audio processing (and image processing, which is the same problem but with another dimension added), is to apply traditional DSP techniques to extract features, then feed them into machine learning algorithms. ML can potentially do awesome things like intelligently inferring missing detail, generating novel sounds, speech synthesis, etc.

It would be great to have the basic DSP stuff ported to pure haxe code and have externs for any highly-optimized already existing functions that would be already present in the environment. As well as implementing basic, easy to implement ml algs such as k-means, knn, naive bayes, etc. in pure haxe and provide externs for what exists in C++ and python.

### Little support for opus format

MP3 and vorbis have readers. Of course wav does. But somehow the format haxelib lacks anything for opus, which is unfortunate when it's such a better format, and meant to be the replacement for vorbis.
