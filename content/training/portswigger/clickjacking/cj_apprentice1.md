+++
date = '2026-07-08T10:18:16+08:00'
draft = false
title = 'Clickjacking with a frame buster script'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
First of all, in order for a clickjacking attempt that changes victim's states to work, anything that requires cookie auth must have that cookie set `SameSite=None`. Thankfully, this is indeed the case for this lab:
![](/images/viber_image_2026-07-08_10-05-08-515.png)
Upon checking the source code, we can see that the developer tries to prevent clickjacking by adding a frame busting script as shown here. What this does is that it checks if the top-most window is the same as the current window. If they are different, it means the page is inside an iframe and the attempt is blocked:
![](/images/viber_image_2026-07-08_10-04-57-242.png)
If we try to use [Burp Clickbandit](https://portswigger.net/burp/documentation/desktop/tools/clickbandit), notice that the attempt to frame the site is blocked:
![](/images/viber_image_2026-07-08_10-07-01-757.png)
To solve this, we should also strip `allow-scripts` so that the framed page's JavaScript will be blocked. `allow-forms` is still kept because we need a form to change emails with (lol)
![](/images/viber_image_2026-07-08_10-07-15-768.png)
After that, use Clickbandit to generate a sample PoC and deliver it to the victim. Neat