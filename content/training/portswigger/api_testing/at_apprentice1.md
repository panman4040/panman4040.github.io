+++
date = '2026-07-09T09:32:16+08:00'
draft = false
title = "Exploiting an API endpoint using documentation | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

When we want to test for APIs, we first need to find out as much information about the API as possible. To do that, we must identify API endpoints. This can be done through reading developer's API doc or fuzzing the paths using Intruder.

In this lab, no doc is provided meaning we have to stick with the latter method. Funnily enough, the documentation endpoint is literally `/api/`, that's it:
![](/images/viber_image_2026-07-09_09-20-28-695.png)

Of course, it would not be as obvious as this in real-work settings. I recommend using [this wordlist](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content/api) to fuzz for valid paths.

Let's try sending an API request. How about querying for the user `carlos`:
![](/images/viber_image_2026-07-09_09-27-32-844.png)
Surprisingly, the server happily sends us back the metadata for that user `carlos`, no strings attached, no auth whatsoever (of course since this is an apprentice lab). Figures that we can also perform `DELETE`, let's try this in Repeater and see the result:
![](/images/viber_image_2026-07-09_09-41-05-521.png)