+++
date = '2026-06-26T15:13:16+08:00'
draft = false
title = 'Reflected XSS into attribute with angle brackets HTML-encoded'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
As with any, we try inserting our canary into the form to see what comes. In this case we will try searching for `ihvkjueb"<>&`
![](/images/6fa9d2f7-ee31-40e8-b661-faadbbfa33bf.jpg)
Notice that while the angle brackets are encoded, `"` isn't. And we can escape the `value` attribute and add it this payload:
```
" autofocus onfocus='alert(1)'
```
This will autofocus the input form right after the page loads, which will trigger `alert` calls indefinitely.