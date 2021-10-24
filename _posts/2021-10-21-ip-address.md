---
title: What can an IP address tell you?
layout: post
image: /public/img/unsplash.network-cable.jpg
categories:
   - blog
tags:
   - info
   - api
   - ip-address
   - location
   - reputation
---

Whenever you visit a website, it is your browser communicating with a web server. 
Your browser knows what server to talk to by taking the domain name, e.g. _mail.google.com_, 
and asking a DNS server for the associated IP address. 
This IP address will tell the browser where to send its network traffic to.

Let's see what that looks like:

    ❯ nslookup mail.google.com                                                                                                                                                                                                                                                      6s
    Non-authoritative answer:
    mail.google.com canonical name = googlemail.l.google.com.
    Name:   googlemail.l.google.com
    Address: 142.251.36.37

This translates to:

> what's the IP address for mail.google.com ?
>
> it's the same as for googlemail.l.google.com !
>
> OK, so what's the IP address for googlemail.l.google.com?
>
> it's 142.251.36.37 !

{%
include img.html
src="/public/img/unsplash.network-cable.jpg"
description="What does my IP address say?"
%}

Every computer that's connected to the internet needs to be able to this kind of interaction 
(which is called a DNS lookup).
These [IPv4 addresses](https://en.wikipedia.org/wiki/IPv4) are a sequence of 4 bytes (4 x a number between 0 and 255), 
and so the total number of possible addresses is 2^32 or about 4.2 billion. 
(there is also [IPv6](https://en.wikipedia.org/wiki/IPv6), 
there is [NAT](https://en.wikipedia.org/wiki/Network_address_translation), 
proxies and [reverse proxies](https://en.wikipedia.org/wiki/Reverse_proxy), but let's not make things too complicated.)

Now, if you know that IP address of a web server, what can you tell from that, without even connecting to it?

## IP lookup

You could do a reverse DNS lookup:

    ❯ nslookup 142.251.36.37
    Non-authoritative answer:
    37.36.251.142.in-addr.arpa      name = ams17s12-in-f5.1e100.net.

Because of the predictable way Google names its servers, you could deduce that the server in question is located in Amsterdam,
which makes sense because it's close to where I am (Brussels, BE). 
Not every hosting company will have the same logic of server naming.
E.g. this website is hosted on Github Pages, and comes from an IP address like `185.199.108.153`. 
A reverse lookup of this address gives `cdn-185-199-108-153.github.com`. This makes one none the wiser.

## IP geolocation

An IP info API like `ipinfo.io` will tell you more about the location and the owner of the IP address:

    ❯ curl ipinfo.io/142.251.36.37
    {
    "ip": "142.251.36.37",
    "hostname": "ams17s12-in-f5.1e100.net",
    "city": "Amsterdam",
    "region": "North Holland",
    "country": "NL",
    "loc": "52.3740,4.8897",
    "org": "AS15169 Google LLC",
    "postal": "1012",
    "timezone": "Europe/Amsterdam",
    "readme": "https://ipinfo.io/missingauth"
    }

## IP reputation

An IP Reputation API will tell you what the reputation is of (the company behind) that range of IP addresses.

    ❯ curl -s ipqualityscore.com/api/json/ip/[KEY]/142.251.36.37
    {
    "success": true,
    "message": "Success",
    "fraud_score": 75,
    "country_code": "US",
    "region": "California",
    "city": "Mountain View",
    "ISP": "Google",
    "ASN": 15169,
    "organization": "Google",
    "is_crawler": false,
    "timezone": "America/Los_Angeles",
    "mobile": false,
    "host": "ams17s12-in-f5.1e100.net",
    "proxy": true,
    "vpn": true,
    "tor": false,
    "active_vpn": false,
    "active_tor": false,
    "recent_abuse": false,
    "bot_status": false,
    "zip_code": "N/A",
    "latitude": 37.39,
    "longitude": -122.08,
    }
