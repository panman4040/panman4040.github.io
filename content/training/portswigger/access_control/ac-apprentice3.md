+++
date = '2026-06-11T10:49:16+08:00'
draft = false
title = 'URL-based access control can be circumvented'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
If we are doing this blind, we can actually append the `X-Original-URL` header (or equivalent) into the request and add a nonsensical endpoint, e.g. `/asdfklsdjflks`. If the server gives us a Not Found, that means the backend code is processing `X-Original-URL` blindly and we can insert our payload, per se.

Thus we can add `?username=carlos` into the query string, then change the `X-Original-Url` path to `/admin/delete`. The `carlos` user will be deleted afterwards.