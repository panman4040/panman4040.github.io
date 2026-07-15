+++
date = '2026-06-23T15:56:16+08:00'
draft = false
title = "DOM XSS in innerHTML sink using source location.search | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We can manually inspect the JS source, or use the DOM invader to help locate our sinks as follow:
![](/images/33b12886-db6b-49c9-a22f-67f35702273d.jpg)

Here our sink is `innerHTML`, note that the innerHTML sink **doesn't accept** `script` elements on any modern browser, nor will `svg onload` events fire. This means we will need to use alternative elements like `img` or `iframe`. Event handlers such as `onload` and `onerror` can be used in conjunction with these elements. 

Luckily, the Exploit option of the DOM Invader comes with a predefined payload:
```javascript
search="'><img src onerror=alert(1)>1'"<>
```

We can simply use this functionality and solve the lab.