---
layout: post
title:  "Automated Testing Haxe Libs on Travis and Gitlab CI"
date:   2018-11-15 07:53:00 -0700
categories: haxe js python nodejs lua c++ c# ci continuous-integration gitlab travis travix lix
---
In being able to target so many other programming languages, Haxe presents a unique challenge for testing. You say your Haxe code is "pure Haxe" and hence compatible with all targets, but to truly know that, you need to actually test on all of the targets. That includes languages I've never used like lua and languages I hope to never have to use again like php.

Of course, there is no substitute for real, actual testing but automated tests are better than nothing and it did help me, for example, fix a weird php-only bug I had (which may have been a bug in the compiler). My libs all use [lix](https://github.com/lix-pm/lix.client) since I like how it does things and hope its the future. And since it's an npm package I can just piggyback on already existing stuff for nodejs people.

On github, I hook up my repos to [Travis CI](https://travis-ci.org/). I actually moved all of my haxe grig (awesome audio lib I'm working on, more info later) repos to gitlab, but have it automatically push mirror to github since travis only supports that and so people searching there can find it. I use almost the same config for all my haxe projects (or [see latest](https://gitlab.com/haxe-grig/grig.midi/blob/master/.travis.yml)):

```yaml
sudo: required
dist: trusty

language: node_js
node_js: 6

os:
  - linux
  - osx
  - windows

install:
  - npm install -g lix

script:
  - lix download
  - if [[ "$TRAVIS_OS_NAME" != "windows" ]]; then haxelib run travix python ; fi
  - haxelib run travix node
  - if [[ "$TRAVIS_OS_NAME" == "linux" ]]; then haxelib run travix js       ; fi
  - if [[ "$TRAVIS_OS_NAME" != "windows" ]]; then haxelib run travix java   ; fi
  - haxelib run travix cpp
  - haxelib run travix cs
  - if [[ "$TRAVIS_OS_NAME" != "windows" ]]; then haxelib run travix php    ; fi
  - if [[ "$TRAVIS_OS_NAME" == "linux" ]]; then haxelib run travix lua      ; fi
```

I take advantage of travis ci's recently-added Windows support, but exclude the tests that I know from experience don't work on Windows (yet) for system config related issues. Travix isn't strictly necessary but it streamlines testing in the various platforms. In addition to using travis, I also use gitlab's ci. For now I just use it for Linux (and I'm using Bionic Beaver instead of ole' Trusty Tahr). For that, I use [this config](https://gitlab.com/haxe-grig/grig.midi/blob/master/.gitlab-ci.yml), almost identical in every repo:

```yaml
image: osakared/haxe-ci
  
before_script:
 - haxelib install hxcpp
 - haxelib install hxjava
 - haxelib install hxcs
 - lix download

test:
  script:
   - haxe tests.hxml --interp
   - haxe tests.hxml -python bin/tests.py           && python3 bin/tests.py
   - haxe tests.hxml -lib hxnodejs -js bin/tests.js && node bin/tests.js
   - haxe tests.hxml -java bin/java                 && java -jar bin/java/RunTests.jar
   - haxe tests.hxml -cpp bin/cpp                   && ./bin/cpp/RunTests
   - haxe tests.hxml -cs bin                        && mono bin/bin/RunTests.exe
   - haxe tests.hxml -php bin/php                   && php bin/php/index.php
   - haxe tests.hxml -lua bin/tests.lua             && lua bin/tests.lua
```

gitlab is flexible in that you can specify the docker image and also configure your own runners. So I made my own image for this purpose ([github repo](https://github.com/osakared/haxe-docker-ci) and [docker page](https://hub.docker.com/r/osakared/haxe-ci/)). As of the writing, the dockerfile is simply:

```Dockerfile
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

# Install Node.js
RUN apt-get update
RUN apt-get install --yes curl gnupg2
RUN curl --silent --location https://deb.nodesource.com/setup_6.x | bash -
RUN apt-get install --yes nodejs npm
RUN apt-get install --yes build-essential software-properties-common python-pycurl python-apt
RUN add-apt-repository ppa:openjdk-r/ppa --yes && apt-get update && apt-get install -y --no-install-recommends openjdk-8-jdk
RUN LC_ALL=C.UTF-8  add-apt-repository --yes ppa:ondrej/php && apt-get update && apt-get install -y --no-install-recommends php7.1 php7.1-mbstring 
RUN apt-get install --yes gcc-multilib g++-multilib python3 mono-devel mono-mcs libglib2.0 libfreetype6 cmake luajit luarocks lua-sec lua-bitop lua-socket libpcre3-dev openjdk-8-jdk openjdk-8-jre
RUN npm install -g lix
RUN luarocks install luasec && luarocks install lrexlib-pcre PCRE_LIBDIR=/lib/x86_64-linux-gnu && luarocks install luv && luarocks install environ && luarocks install luautf8

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64

CMD ["/bin/bash"]
```

You can easily modify this or make your own Dockerfile that extends this (`FROM osakared:haxe-ci`).