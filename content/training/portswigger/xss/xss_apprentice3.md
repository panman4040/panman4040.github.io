+++
date = '2026-06-24T09:21:16+08:00'
draft = false
title = 'DOM XSS in jQuery anchor href attribute sink using location.search source'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
When we check out the Submit Feedback, notice that there is a `returnPath` query string hanging out in the URL.
![](/images/488824d4-a929-4163-8456-4581174e9e13.jpg)

If we look closer to the source code, we find out that this `returnPath` handles the redirect after clicking on the Back button, evident by its `#backLink` ID.

The JS code naively takes whatever value of `returnPath` inside the `window.location.search` sink and add that as an attribute of the `a` tag. We can then insert this payload inside the URL:
```javascript
?returnPath=javascript:alert(document.cookie)
```
This will alert the `document.cookie` once we decide to go back, thus solving the lab. Though jQuery is less common now, this lab helps strengthen our pentesting intuition which is always to look out for subtle details like this.
