+++
date = '2026-06-26T14:19:16+08:00'
draft = false
title = "Reflected XSS with some SVG markup allowed | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Similar to [this problem](/content/training/portswigger/xss/xss_expert1.md), we first try to find out which tags are allowed and which are not using Intruder as follow:
![](/images/0846e732-2482-4b38-9e18-3d6cb61048d1.jpg)
The tags available are `animateTransform`, `image`, and `svg`. `title` is also allowed but it doesn't assist much in delivering payloads so we will skip it for now.

So we are given three building blocks. One may craft a payload as follow:
```javascript
<svg><image width=10 height=10 src/onerror=alert(1)></svg>
```
But it doesn't work. Using `animateTransform` to add the event indirectly also does not work. Using other events does not work. Once again, we use Intruder to brute our way to which events are accepted and which aren't:
![](/images/14de7ba5-1454-40a4-bf66-00937ae7bc62.jpg)
All but one event is blocked, and that is `onbegin`. It is essentially `onload` but for SVG animation elements. So we can craft a payload as follow:
```javascript
<svg><image><animateTransform onbegin='alert(1)'/></image></svg>
```