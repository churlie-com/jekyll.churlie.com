---
title: What can a web server tell you?
layout: post
image: /public/img/unsplash.server.jpg
categories:
   - tech
tags:
   - info
   - api
   - host
   - server
---
When your browser communicates with a web server, some information is exchanged even before the first `<html>` code is received. Like what, for instance?

{%
include img.html
src="/public/img/unsplash.server.jpg"
description="What does my web server say?"
%}

## TLS security

Your website will most certainly be a `https://` (_secure_) website. This means that there is already a whole conversation, just to agree on the end-to-end encryption that will be used from then on. This is what that looks like:

```
$ curl -ivk https://mail.google.com
*   Trying 172.217.168.229...
* TCP_NODELAY set
* Connected to mail.google.com (172.217.168.229) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/cert.pem
  CApath: none
* TLSv1.2 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-ECDSA-CHACHA20-POLY1305
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=mail.google.com
*  start date: Oct  4 02:41:15 2021 GMT
*  expire date: Dec 27 02:41:14 2021 GMT
*  issuer: C=US; O=Google Trust Services LLC; CN=GTS CA 1C3
*  SSL certificate verify ok.
```

By the end of that conversation, we know that the server mail.google.com has a certificate from Google Trust Services that proves it's really that server (and not a fake spoofing server). 

## Server type

The rest of that conversation goes like this:

    < HTTP/2 301
    < location: /mail/
    < expires: Sun, 24 Oct 2021 13:51:01 GMT
    < date: Sun, 24 Oct 2021 13:51:01 GMT
    < cache-control: private, max-age=7776000
    < content-type: text/html; charset=UTF-8
    < x-content-type-options: nosniff
    < x-frame-options: SAMEORIGIN
    < content-security-policy: frame-ancestors 'self'
    < x-xss-protection: 1; mode=block
    < server: GSE
    < alt-svc: clear
    < accept-ranges: none
    < vary: Accept-Encoding

* the server works with HTTP 2.0, not HTTP 1.1 => it's a modern server
* the server has content security specifications like `content-security-policy` and `x-xss-protection`, again a sign that it's an up-to-date setup
* the server type = GSE, which stands for `Google Servlet Engine`, an indication that the website is run by Google (as if you needed more proof)
* the URL is a 301 permanent redirect to `https://mail.google.com/mail/`
