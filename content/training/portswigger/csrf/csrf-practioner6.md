+++
date = '2026-06-05T09:41:16+08:00'
draft = false
title = 'SameSite Lax bypass via method override'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Upon inspecting the HTTP response header, we see that `SameSite` isn't explicitly set to anything. We can safely assume that it's set to `Lax` by default (at least in Chrome)
![](/images/6b6bc548-ea79-4264-9780-20356717c3c6.jpg)

While intercepting the request for email change as a normal user, we can see that there is no CSRF token validation which makes our payload lighter:
![](/images/5f1e4a15-6e0c-4832-bd6e-4d100d9c79f3.jpg)

Our initial payload is this:
```html
<script>
    document.location = 'https://<lab-id>.web-security-academy.net/my-account/change-email?email=attack%40hack.net';
</script>
```
But once we test this on ourselves, we are hit with a 405 Method Not Allowed. Viewing the page source also confirms the form only accepts `POST` request. Thus we have to override the method in our request line:
```html
<script>
    document.location = 'https://<lab-id>.web-security-academy.net/my-account/change-email?_method=POST&email=attack%40hack.net';
</script>
```
Note that `_method` is only specific for frameworks such as Symfony, Laravel, or NodeJS. The param names differ when using other frameworks, for example `_METHOD` or `x-method-override`.
