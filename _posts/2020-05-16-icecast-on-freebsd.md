---
layout: post
title:  "Setting up Icecast Streaming server on FreeBSD"
date:   2020-05-16 08:09:59 -0800
categories: freebsd icecast liquidsoap mp3 ogg
---
I started with [these instructions](https://www.vultr.com/docs/radio-streaming-on-freebsd-10-with-icecast-and-ices) for just the icecast part but updated for FreeBSD 12.1. But left out the source client stuff I didn't need (IceS is too limited features for me) and added the (very impotant imo!) ssl stuff to keep things secure. Install icecast:

`pkg install icecast`

Enable it:

```sh
echo 'icecast_enable="YES"' >> /etc/rc.conf
```

Start with default config, then edit

```sh
cd /usr/local/etc
cp icecast.xml.sample icecast.xml
```

I started with the [instructions on certbot's site](https://certbot.eff.org/lets-encrypt/freebsd-nginx.html) and [here](mediarealm.com.au/articles/icecast-https-ssl-setup-lets-encrypt/), however I'm personally using this on an instance that will be spun up and stopped so I won't bother putting the ssl renewal in cron and just renew manually as needed. When you install certbot, there are better instructions for making renewal automatic, but again I'm not going to do that here. Install certbot:

`pkg install py36-certbot`

So for me, I just manually obtain a cert for the domain (http needs to be accessible for this, not just https):

`sudo certbot certonly --standalone`

Then combine the files to create a pem suitable for icecast.

`cat /usr/local/etc/letsencrypt/live/DOMAIN/fullchain.pem /usr/local/etc/letsencrypt/live/DOMAIN/privkey.pem > /usr/local/share/icecast/icecast.pem`

Go to the `authentication` section and change passwords to something unique. I definitely recommend not logging in to admin from a browser until you finish the ssl step. Uncomment the `changeowner` section under `security`. I would also add values to location and admin to prevent warnings about that. Put the right value in hostname. Go to listen-socket and comment/uncomment/change so that only 443 with ssl on is enabled. Save and exit.

Enable logging by making the right dir with the right ownership:

```sh
mkdir /var/log/icecast
chown nobody:nogroup /var/log/icecast
```

Start icecast:

`service icecast start`

If there are warnings you want to tend to, go ahead and re-edit the config file and restart (`service icecast restart`).

I used to use nginx with reverse-proxy but this caused some problems like streams crapping out after an hour. icecast might not have as much built-in security measures but it's special-purpose made for streaming so I think better overall.

I also found, unfortunately, that mixx [still doesn't support ssl](https://bugs.launchpad.net/mixxx/+bug/1517087) as of this writing. But you can always use jack to connect it to something that can. [Butt](https://danielnoethen.de/butt/), on the other hand, does work. Simply fill in the url, port 443 and the source username and password works for me.

**However** I've found butt to be very crashy and couldn't even switch audio source without it freezing in linux. But in looking for alternatives, found something even better! Liquidsoap also supports https icecast2 streaming:

```sh
liquidsoap 'output.icecast(%mp3, host="DOMAIN", port=443, password="PASSWORD", mount="radio", protocol="https", mksafe(playlist("playlist.m3u")))'
```
