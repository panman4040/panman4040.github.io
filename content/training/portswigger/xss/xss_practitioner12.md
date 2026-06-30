+++
date = '2026-06-30T13:48:16+08:00'
draft = false
title = 'Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

(lab's name is a bit of a handful)

As usual, let's first insert our canary into the search form to see what's the buzz:
![](/images/8b3f77d0-ea39-4bcc-9cd8-58dfb8b7bfaa.jpg)

While all our special characters are encoded thus no traditional payloads, notice that the page is trying to build the message dynamically using client-side JS. And **backticks** are used rather than single or double quotes, meaning we can probably inject JavaScript template literals within the message itself.

By inserting `${alert(1)}`, we are greeted with this:
![](/images/b80aaf52-04ce-4788-a0b4-8686c63ebecb.jpg)
The payload works and an `alert` is triggered! Very interesting.