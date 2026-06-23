+++
date = '2026-06-23T14:57:16+08:00'
draft = false
title = 'DOM XSS in document.write sink using source location.search inside a select element'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

![](/images/a3aa6e42-5a76-45ee-8f10-20e2e44b8582.jpg)
Upon viewing the source, we see that this script has a `document.write()` sink that writes out the select form in the HTML. It queries for the value of `storeId` param and attaches it to an `<option>` tag as shown.

Upon attaching a `storeId` query param into the URL, the DOM Invader captures our random string present within the HTML, and confirming that the sink in question is indeed `document.write()`:
![](/images/b6a5e5a0-24fc-46ea-afff-c4a4674c1a94.jpg)

We can then simply set `storeId=<script>alert(1)</script>` and it works fine, but weirdly this lab requires us to get out of the select element. To solve this, we prepend `</select>` at the beginning of our payload.