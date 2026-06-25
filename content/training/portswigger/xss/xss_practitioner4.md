+++
date = '2026-06-25T14:30:16+08:00'
draft = false
title = 'Stored DOM XSS'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Really took a long time with this one due to not knowing enuf JS syntax

Upon closer inspection of the source, the comment body seems to be added into an `innerHTML` sink for display, which is a huge red flag opening up to Stored XSS payloads.
![](/images/a83c7806-d24b-405e-9716-0acb106d9cd6.jpg)
There is a peculiar custom function `escapeHTML()` which does as it says on the tin. It filters out `<` and `>`, effectively blocking most XSS payloads, at least in theory.
![](/images/f5d66836-c862-4e7b-b27c-1bbd9f609be1.jpg)
This only encodes the first occurences of those two brackets, which mean our payload can simply be:
```javascript
<><img src=1 onerror=alert(1)>
```
The first two angle brackets will be encoded, but any subsequent angle brackets will be unaffected.