+++
date = '2026-06-08T14:15:16+08:00'
draft = false
title = "Username enumeration via response timing | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Bypass IP block while brute forcing'
showLastUpdated = true
+++
First I thought this is a straightforward lab: just pump into Intruder and compare response received times. But the lab also implements some sort of IP-based brute-force protection, where if you guess too many incorrect attempts, it will lock you out.

To solve this, we shall use the `X-Forwarded-For` header, which is used to identify the source IP address connecting through a proxy or load balancer. This is good for redirecting traffic but opens up to spoofing. If we are locked out, we just need to tweak the IP address every now and then, and include our valid credentials occasionally in our payload to avoid being locked out.

Or we can dynamically change the forwarded IP address for every enumeration, simply select the Pitchfork attack then change the first position of the IP for each login attempt.
![](/images/5ca163b0-f00e-435c-95f4-5a0daf06b2d5.jpg)
Notice that `accounting` took significantly longer than other usernames. Given that we set the password to be absurdly long, we can safely assume that's our valid username. Similarly, we brute-force the password for `accounting` to solve the lab.
![](/images/b39ab4ce-d42b-4719-86ad-a75e07add44c.jpg)
