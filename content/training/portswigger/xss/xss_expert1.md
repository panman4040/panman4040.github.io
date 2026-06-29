+++
date = '2026-06-26T10:03:16+08:00'
draft = false
title = 'Reflected XSS with event handlers and href attributes blocked'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'My first Expert lab! Quirk regarding svg'
showLastUpdated = true
+++
Similar to previous problems, first we shall pass through Intruder to see which tags are accepted
![](/images/6c22fc95-c607-4ae9-ab57-bf67430d6620.jpg)
Some of the valid tags are `<a>`, `<animate>`, `<image>`, and `<svg>`. This is a huge flag for an `svg` tags ecosystem. Since the `href` attribute is blocked by the WAF, perhaps somehow we can sneak that through indirectly without alerting the firewall?

The answer is using the `<animate>` tag, with its convenient `attributeName` and `values` attributes, it allows us to indirectly modify the parent tag's attributes and their values. Thus we can craft a payload as follow:
```javascript
<svg>
    <a>
        <animate attributeName=href values=javascript:alert(1) />
            <text x=20 y=20>Click me</text>
    </a>
</svg>
```

Note that this payload requires the victim to click the damning link, hence the Click me.