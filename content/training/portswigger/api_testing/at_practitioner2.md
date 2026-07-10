+++
date = '2026-07-09T13:23:16+08:00'
draft = false
title = 'Exploiting a mass assignment vulnerability'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Our task is to buy that leet jacket, but our coffer runs dry. Just like [last lab](/training/portswigger/api_testing/at_practitioner1), we have to either raise our balance or make the jacket free.

While crawling through the page, I tried sending a request to `/api`, literally. To my surprise, there actually is a hidden documentation lying around:
![](/images/viber_image_2026-07-09_11-03-23-248.png)
The GET request to `/checkout` seems to request the metadata for our purchase, with these attributes lying around. While the POST request is performing that purchase. So after adding the leet jacket to our cart, let's perform the GET request to see the response:
![](/images/viber_image_2026-07-09_11-08-11-613.png)
The server returns a JSON object with very clear attributes. One thing to note though is that the `chosen_discount` attribute is left conveniently there for us, while no explicit option for discount is present on the page (perhaps it's for a later sales period?). Let's try sending the discount attribute along with our purchase in a POST request to `/checkout`, with say 100% discount rate? Let's try this out:
![](/images/viber_image_2026-07-09_11-08-38-249.png)
Looks like the request has gone through, and we got ourselves a slick jacket free of charge. Nice.
![](/images/viber_image_2026-07-09_11-08-56-955.png)