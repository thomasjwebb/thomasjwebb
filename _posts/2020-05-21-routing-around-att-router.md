---
layout: post
title:  "Fixing Issue With SSL Traffic on AT&T Router"
date:   2020-05-21 17:44:22 -0700
categories: ssl att router static
---
My issue is [detailed here](https://stackoverflow.com/questions/61893382/issue-with-ssl-traffic-originating-from-home-network-destined-to-home-server-usi) but I thought I'd summarize here basically how to get around it.

Turns out this is an issue with the BGW210 and port 443. If I do the same setup but on 8443 or something else, https works fine. Looking further, it turns out this is is a chronic issue with this and possibly other routers supplied by AT&T (see [here](https://forums.att.com/conversations/att-fiber-equipment/port-443/5df01162bad5f2f60648d0aa?page=1), [here](https://forums.att.com/conversations/att-internet-equipment/arris-bgw210700-being-blocked-with-disallowed-wanside-management-service-access/5df03076bad5f2f6062cfbdf) and [here](https://forums.att.com/conversations/att-internet-equipment/bgw210-port-forwarding-dropping-most-packets-to-specified-port/5defc724bad5f2f606308b9b)). Trying to use IP forwarding or DMZ settings on the router doesn't save whatever's behind it from whatever filtering it's doing. None of the firewall settings work.

Since I already have static IP, I was able to bypass the filtering and effectively use the BGW as a modem and my own router as the router by turning on **public subnet mode**. To do this:

 1. Get all your static IP, default gateway, netmask settings from AT&T if you don't already have it.
 2. Go to Home Network -> Subnets & DHCP
 3. Turn **public subnet mode** and **allow inbound traffic** on. Set **primary DHCP pool** to public.
 4. Put the info from AT&T into the fields after that as appropriate. I also don't set the BGW itself to use any of the static IPs since I'm not using it to do NAT for any servers.
 5. Setup your own router of choice with the settings you want, then turn it off, plug its wan port into one of the BGW's ethernet ports. Turn it on.
 6. Connect server to this router rather than the BGW and setup NAT on it.

Now the BGW's filtering is bypassed and it's now your own router's job to handle NAT for your server. This also offers the advantage that you're not stuck with the BGW's limited feature set. If you have a good router with DD-WRT on it, you can setup your own DNS, VPN, etc. And yes, this thoroughly solves the port 443 issue.

**Beware that anything you connect via ethernet or wifi to the BGW will be exposed directly to the outside world**. I would only plug in routers with firewalls since, again, you're bypassing the BGW's firewall. I kept the wifi turned on in case I need to directly connect for diagnostics, but removed automatic connect to it from all computers/changed wifi password.

