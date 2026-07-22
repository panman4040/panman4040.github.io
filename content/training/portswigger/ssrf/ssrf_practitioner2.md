+++
date = '2026-07-22T09:26:16+08:00'
draft = false
title = "SSRF with filter bypass via open redirection vulnerability | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

This lab has a stock check feature which interestingly fetches data from an internal system, here is an example of an intercepted stock check request:
![](/images/viber_image_2026-07-22_09-06-22-271.png)

Let's try inserting something arbitrary into the `stockApi` param to see whether it still honors our request or not:
![](/images/viber_image_2026-07-22_09-12-22-773.png)
Looks like the filter is a lot more rigorous this time, in fact if it doesn't start with `/product`, the server will block our request. Seems like they only allow the stock checker to access the **local application** only, so maybe we can look elsewhere for an open redirection vulnerability.

After scrolling for a while, we should see a button thay says "Next product", sounds fishy. Let's try intercepting the request to see what's up:
![](/images/viber_image_2026-07-22_09-11-14-866.png)

Looks like there is a `path` param that redirects us to whatever product is next, in this case it's `/product?productId=2`. But is it restricted to local `/product` only? Let's try putting on something arbitrary, for example literally `arbitrary`:
![](/images/viber_image_2026-07-22_09-13-27-897.png)
Unlike the `stockApi` check, the validation for `path` is very tolerable and honors all our redirection attempts, whether local or not. Can we utilise this and make a request to `http://192.168.0.12:8080/admin`?
![](/images/viber_image_2026-07-22_09-14-14-857.png)
Looks like we can! Though we still have to redirect manually to that private IP address which is impossible on this end. But remember that stock check feature? Turns out since this open redirection vulnerability happens on the local application, the backend accepts our request and forwards us to the admin interface. See for yourselves:
![](/images/viber_image_2026-07-22_09-24-03-728.png)