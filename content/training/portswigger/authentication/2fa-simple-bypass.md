+++
date = '2026-06-02T16:14:16+08:00'
draft = false
title = "2FA simple bypass | <span style=\"color:#2ecc71\">Apprentice</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
This excerpt from PortSwigger sums this up pretty nicely:

"At times, the implementation of two-factor authentication is flawed to the point where it can be bypassed entirely.

If the user is first prompted to enter a password, and then prompted to enter a verification code on a separate page, the user is effectively in a "logged in" state before they have entered the verification code. In this case, it is worth testing to see if you can directly skip to "logged-in only" pages after completing the first authentication step. Occasionally, you will find that a website doesn't actually check whether or not you completed the second step before loading the page."

So after we log in using `carlos` credentials, we can just skip straight to the main page while still logged in. The server does not bother to check whether we've passed 2FA or not. Very informative.

