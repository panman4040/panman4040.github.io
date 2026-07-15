+++
date = '2026-06-05T10:22:16+08:00'
draft = false
title = "SameSite Strict bypass via client-side redirect | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Very interesting path traversal problem'
showLastUpdated = true
+++
Unlike [this problem](/training/portswigger/csrf/csrf-practioner6.md), the cookies are set to be `SameSite=Strict`:
![](/images/e92e5e03-4beb-498f-850b-7a2d4c5cdae8.jpg)
However, CSRF protection is still not in place, so maybe we can figure out an exploit.

There is a peculiar feature in the site itself, whereby once you post a comment, there will be a confirmation page that redirects you back to the post you've made the comment on
![](/images/6d7091df-1976-446c-b09a-e974a54cb598.jpg)

`postId` is the only but required parameter, we can traverse up the path by setting `postId = ../` and surely enough it redirects us back to the main page. Note that the action of posting comments and being redirected does **not** required you to be logged in.

Thus we can craft a payload around `/post/comment/confirmation?postId=...`:
```html
<script>
    document.location = 'https://<lab-id>.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email%3femail%3dattack%40hack.net%26submit%3d1';
</script>
```
Note that there is a new required `submit` parameter that must be 1. We use URL-encoding for the parameters required for changing emails so as to avoid mixing it up with the parameters for `confirmation` - a path traversal key point. Very interesting problem.