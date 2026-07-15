+++
date = '2026-06-23T14:38:16+08:00'
draft = false
title = "DOM XSS in document.write sink using source location.search | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
This is quite a straightforward lab, but I want to emphasize on the use of Burp Browser's DOM Invader.

![](/images/8f9f7604-672e-475b-b7d3-7a72f6021903.jpg)

The "canary" here is `pp591rlt`, which is an unpredictable and uncommon string that we can inject into different sources to see which sinks they flow into. Included in the string are special characters `"`, `<`, and `>`. This helps test whether the browser will sanitize the quotes and brackets or not. 

In this example, we find out that the canary flowed into the `document.write (1)` sink, which is an HTML sink as it directly modifies HTML in the page. The "Value" column shows where our payload has landed in the code. Essentially, the JS code is trying to do this:
```javascript
document.write('<img src="/resources/images/tracker.gif?searchTerms=' + your_search + '">');
```
The special characters are not sanitized, that's why `">` appear as stray text as shown. That means we can craft an XSS payload without being filtered by the backend. Click on the "Exploit" button to send out a simple `alert()` payload.