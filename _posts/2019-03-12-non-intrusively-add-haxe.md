---
layout: post
title:  "Non-Intrusively Adding Haxe to Your Javascript Setup"
date:   2019-03-12 19:03:27 -0700
categories: haxe js netlify npm
---
Installing haxe is very easy coming from the javascript side. In fact, the way I recommend to install haxe generally is to use [lix](https://github.com/lix-pm/lix.client), which is an npm package (also available on yarn). The only time I'd advise against that is if you don't want to have npm on your system.

So if you already have a directory with a `package.json` (if not, run `npm init`), then you can install lix locally:

```bash
npm install --save lix
```

Generally I recommend installing globally (-g), but you don't have to to be able to use it. So if you just want to try out haxe in one js project, you can install it locally, then use `npx` to run lix. To create a `.haxerc` with the latest version of haxe:

```bash
npx lix scope create
```

You can also install different versions of haxe if you wish and that will modify `.haxerc` accordingly. For example, to get the nightly build:

```bash
npx lix install haxe nightly
```

Note that this will change .haxerc to point to the nightly at the time you run it. You must run this again if you wish to update again, as you'd probably expect. The haxe compiler itself is also available through `npx` as `lix` provides a shim for that. Same thing for `haxelib`. The first time you run haxe, it will download the appropriate version for you automatically:

```bash
npx haxe
```

Go ahead and install whichever haxe packages you need (maybe externs for react?) Doing so creates entries under `haxe_libraries` and also downloads the packages. The contents of `haxe_libraries` *do* belong under source control. They simply contain metadata about version and where the package is located:

```bash
npx lix install haxelib:react
```

So if you obtained something that already has `haxe_libraries` but contains libraries you didn't install yourself already, you'll need to let `lix` know to download them:

```bash
npx lix download
```

You can also ensure that this is ran when you run `npm install` by adding it to `package.json`:

```json
"scripts": {
    "postinstall": "lix download"
}
```

lix's [github page](https://github.com/lix-pm/lix.client) has more information, but with just what you have here, you can easily sneak haxe into any place designed to work with javascript projects. It's how I got my haxe-based server-side rendering integrated into netlify. Maybe I'll post about that soon.

Seeing how well lix brings haxe into javascript's world makes me wish lix could also be built as a python script (it is written in haxe after all, but relies on node externs). If only...