+++
date = '2026-07-03T15:39:16+08:00'
draft = false
title = "Manipulating the WebSocket handshake to exploit vulnerabilities | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Hell of a lab, highly suggest reviewing'
showLastUpdated = true
+++
Since the only useful functionality on the lab is the live chat feature, we will be trying out payloads on it, but this proved much harder than I thought (lol).

After sending the WebSocket message to Repeater and crafting a simple `alert` payload, we are immediately IP banned with no room to spare (damn).
![](/images/301b9b8d-fd58-4c94-83bb-a5d4d52610e9.jpg)
Upon inspection of the WebSocket history, we should see that the server detects our payload as an attack and acts accordingly:
![](/images/2feb5ab7-2051-4c54-a515-25006951804d.jpg)

Thankfully, we can *spoof* our requests using the `X-Forwarded-For` header (which should be infeasible in real prod as every reverse proxy nowadays filters custom headers anyway). But this means for every request we need to copy paste the header at the bottom, tedious but still. 
![](/images/76cd020a-0dc7-455e-976b-c7a8b41a0571.jpg) 

We still have that payload to deliver. One thing to note is that the application encodes angle brackets, quotes, etc. on transit. That means for every request we have to intercept it and insert our payload then, supported by the fact that we are IP banned shows that the payload is valid and can be executed.
![](/images/32de463c-a7ca-4eb4-aab8-01ba86cdbbdc.jpg)

Okay so we have to insert the `X-Forwarded-For` header for every request, and intercept our WebSocket message during transit to bypass the character encoding. But we still yet to know what is triggering the ban. The original error message was `Attack detected: Alert`, so maybe the backend checks for keywords? Let's find out
![](/images/b9bc3581-6c7d-4738-8a56-f4eda0c44a5e.jpg)

Okay `alert` works fine, so does `ONERROR=ALERT`. So keywords are not the problem. Or it is indeed the problem, but maybe the filter is looking for **exact strings**. Meaning our event `ONERROR=ALERT` should be blocked but isn't. Keep in mind since HTML attributes are not case-sensitive, whatever nonsense we capitalise *will work* regardless. We can then craft our payload as follow:
```html
<img SRC=1 ONERROR='alert(1)'>
```
Nope, still blocked. So the keywords are not the only problem here. JavaScript is not case-sensitive so we cannot do anything about `alert`. Maybe it's because the backend sees the sensitive word `alert` is right next to characters like quotes or parenthesis, and immediately flags the attempt as an attack. What can we do about this?

After some time, I realise that the new JavaScript (ES6) now allows us to execute functions or pass strings without those damned characters. We can instead use **backticks** instead. One can only hope that the backend is not updated with this revelation. Thus our new payload is:
```html
<img SrC=1 OnErroR=alert`1`>
```
![](/images/ef76b857-a80e-48bd-954e-0282b5c971c8.jpg)
Server responses with how the message is displayed rather than blocking it, huge green flag for us. And upon checking the live chat, we will be hit with an `alert`. Lab solved!
![](/images/8e715f4d-ccc4-4b25-91d7-093e76fa8e2b.jpg)
