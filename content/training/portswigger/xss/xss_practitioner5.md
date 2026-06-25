+++
date = '2026-06-25T14:59:16+08:00'
draft = false
title = 'Reflected XSS into HTML context with most tags and attributes blocked'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
If we try a standard XSS payloads using `img` or `script`, the built in WAF will block the request. We can try other tags manually, or we can use Burp Intruder to first do a sweep of which tags are accepted, and which aren't. You can find a list of common XSS payloads on PortSwigger's XSS Cheat Sheet
![](/images/c71ce16b-f3bc-46b5-8ae4-00b2f9511b7d.jpg)
Notice the only the `body` tag returns a 200. We shall use this information and narrow our attribute range for the tag.
![](/images/fd571378-8707-4aef-be61-878579a44ada.jpg)
There are a few attributes accepted, notably being `onresize`. Without needing user interaction, this is a holy grail among attributes used for XSS payloads. With that said, we can craft a payload as follow:
```html
<iframe src="https://<lab-id>.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```
`this.style.width='100px'` forces the iframe to shrink to 100 pixels wide, which triggers our `onresize` event. Very noteworthy challenge.

Note: `oncontentvisibilityautostatechange` also works with an even simpler payload, but the lab says nope for some reason (lol)