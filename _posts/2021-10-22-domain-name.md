---
title: What can a domain name tell you?
layout: post
image: /public/img/unsplash.com.jpg
categories:
   - tech
tags:
   - info
   - api
   - domain
---
According to [Verisign](https://www.verisign.com/en_US/domain-names/dnib/index.xhtml?section=executive-summary)
there are about 370 million domain names registered in total in 2021. 
A domain name like `google.com` inspires trust, a name like `westealyourdata.tk` totally not,
(although one could argue both seem to have the same business model)
but there's a large gray zone between those extremes. So what can you know about a domain name before you even visit it?

{%
include img.html
src="/public/img/unsplash.com.jpg"
description="What does my domain name say?"
%}

## TLD

The domain suffix or TLD (Top Level Domain) can tell you something about the scope of the domain.
A `something.de` domain has a big chance of 1) being written in only/mostly German and 2) aiming to serve customers in Germany.
If a company wants to show that its scope is more global, 
they will typically opt for a non country-related TLD like `.com`, `.net`, `.io` etc ...
There are also conventions like `.mil` = military, `.org` = non-profit, `.ac.be` = Belgian academia.
With new TLDs being introduced every year, you need an API to verify that a TLD exists and what it stands for.

## Domain Whois

Every domain was registered by someone at some point in time, 
and the domain naming system has always had a way to look up these details: [the WHOIS protocol](https://en.wikipedia.org/wiki/WHOIS).
It used to be that you could find names and email addresses this way. 
However, for privacy and anti-spam reasons, this information is more often hidden these days.

    ❯ whois forret.com
    % IANA WHOIS server
    % for more information on IANA, visit http://www.iana.org
    % This query returned 1 object
    (...)
    # whois.gandi.net 
    Domain Name: forret.com
    Registry Domain ID: 16687460_DOMAIN_COM-VRSN
    Registrar WHOIS Server: whois.gandi.net
    Registrar URL: http://www.gandi.net
    Updated Date: 2020-12-06T14:32:49Z
    Creation Date: 2000-01-06T15:27:42Z
    Registrar Registration Expiration Date: 2026-01-06T15:27:42Z
    Registrar: GANDI SAS
    Registrar IANA ID: 81
    Registrar Abuse Contact Email: abuse@support.gandi.net
    Registrar Abuse Contact Phone: +33.170377661
    Domain Status: clientTransferProhibited http://www.icann.org/epp#clientTransferProhibited
    Registry Registrant ID: REDACTED FOR PRIVACY
    Registrant Name: REDACTED FOR PRIVACY
    Registrant Organization: forret.com bvba
    Registrant Street: REDACTED FOR PRIVACY
    (... everything is REDACTED FOR PRIVACY)
    Tech Phone: REDACTED FOR PRIVACY
    Tech Email: 8857f994a55dbb9435c72ee4e17a89dc-1832657@contact.gandi.net
    Name Server: NS-70-A.GANDI.NET
    Name Server: NS-186-B.GANDI.NET
    Name Server: NS-141-C.GANDI.NET
    URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
    >>> Last update of WHOIS database: 2021-10-21T20:48:40Z <<<

Whois also used to tell you when a domain was going to expire. 
Because this info is often misused by domain speculators and scammers, some DNS registries even hide that. 
E.g. DNS Belgium, that holds the info for `.be` and `.eu` domains

    ❯ whois forret.be
    whois:        whois.dns.be
    status:       ACTIVE
    remarks:      Registration information: https://www.dnsbelgium.be
    created:      1988-08-05
    changed:      2021-03-22
    source:       IANA
    Domain: forret.be
    Status: NOT AVAILABLE
    Registered:     Mon Dec 18 2000
    Registrant:
    Not shown, please visit www.dnsbelgium.be for webbased whois.
    Registrar Technical Contacts:
    Organisation:   Gandi SAS
    Language:       fr
    Phone:  +33.170377661
    Registrar:
    Name:    Gandi Sas
    Website: https://www.gandi.net/domain/buy/result/
    Nameservers:
    ns-240-a.gandi.net
    ns-61-c.gandi.net
    ns-68-b.gandi.net

The registration date of a domain is always available. If a domain was only created 1 month ago, 
and it belongs to a company that claims to be in business for a decade, that might be a red flag.

## Domain NS/SOA

There is a way for any DNS server to find out: who knows about all the possible subdomains of `whatever.com`.
This is by doing an NS lookup via the DNS protocol: what are the nameservers for this domain?
In some cases, this might give you a hint of who owns or hosts the domain.

    ❯ dig oculus.com in ns
    (...)
    ;; QUESTION SECTION:
    ;oculus.com.                    IN      NS
    
    ;; ANSWER SECTION:
    oculus.com.             1800    IN      NS      c.ns.facebook.com.
    oculus.com.             1800    IN      NS      d.ns.facebook.com.
    oculus.com.             1800    IN      NS      b.ns.facebook.com.
    oculus.com.             1800    IN      NS      a.ns.facebook.com.

For instance, here you see that the NS servers for oculus.com are those of Facebook.
If you didn't already know, Oculus was bought by Facebook. 
At some point, you would have seen that this NS information changed from what it was before, to Facebook.

There is also the SOA (Start Of Authority) data that can be requested via DNS. 
This also gives you an email address, which might also point to an owner/hoster.

    ❯ nslookup                                                                                                                                                                                                                                                               5m 58s
    > set type=soa
    > oculus.com
    Server:         192.168.1.1
    Address:        192.168.1.1#53
    
    Non-authoritative answer:
    oculus.com
    origin = a.ns.facebook.com
    mail addr = dns.facebook.com
    serial = 3774020839
    refresh = 7200
    retry = 1800
    expire = 604800
    minimum = 120

## Domain reputation

A domain that has been around for a while has a history, and that history might be tainted.
If the domain was used to send spam, to initiate phishing attacks, etc, the reputation goes down.
If this domain is actually yours, you might want to know about this the moment it happens.

    ❯ curl -s "https://domain-reputation.whoisxmlapi.com/api/v1?apiKey=[KEY]&domainName=facebook.com" | jq                                                                                                                                          5s
    {
        "mode": "fast",
        "reputationScore": 98.11,
        "testResults": [
            {
                "test": "WHOIS Domain check",
                "testCode": 93,
                "warnings": [
                    "Owner details are publicly available"
                    ],
                "warningCodes": [
                    2009
                    ]
            },
            {
            "test": "SSL vulnerabilities",
            "testCode": 88,
            "warnings": [
                "HTTP Strict Transport Security not set",
                "TLSA record not configured or configured wrong",
                "OCSP stapling not configured"
                ],
            "warningCodes": [
                6015,
                6019,
                6021
                ]
            }
        ]
    }

Some APIs look up the domain in a large collection of reputation databases and give you a combined score.

    ❯ curl "https://endpoint.apivoid.com/domainbl/v1/pay-as-you-go/?key=[key]&host=something.com"
    {
    "data": {
    "report": {
    "host": "something.com",
    "blacklists": {
    (...)
    "detections": 1,
    "engines_count": 47,
    "detection_rate": "2%",
    "scantime": "0.19"
    },
    (...)
    "category": {
        "is_free_hosting": false,
        "is_anonymizer": false,
        "is_url_shortener": false,
        "is_free_dynamic_dns": false
        },
    "security_checks": {
        "is_most_abused_tld": false,
        "is_domain_blacklisted": true,
        "is_uncommon_host_length": false,
        "is_uncommon_dash_char_count": false,
        "is_uncommon_dot_char_count": false,
        "website_popularity": "low",
        "is_uncommon_clickable_domain": false,
        "is_risky_category": false
        },
    "risk_score": {
        "result": 0
        }
    }
    },
    "elapsed_time": "0.41",
    "success": true
    }
