+++
date = '2026-07-16T10:57:16+08:00'
draft = false
title = "Visible error-based SQL injection | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Similar to [this lab](/training/portswigger/sqli/sqli_practitioner7), an SQLi vulnerability exists within `trackingId`. But this time, the server returns "helpful" error messages in its response. Take a look at the response when I tried to inject `' AND 1=1` (ending single quote deliberately excluded):
![](/images/viber_image_2026-07-16_10-37-20-841.png)

The server literally hands out the entire server-side query being executed. Since we know there is an `users` table, we can just perform a `SELECT` query for the `administrator` user right? Turns out there's actually a character limit, and if our injected query gets too long then it will be truncated:
![](/images/viber_image_2026-07-16_10-41-53-026.png)

So our goal is to craft an injected query that's not too long, while still doing its job. PortSwigger hints at using the `CAST()` function to achieve this. For example, this query attempts to convert a string to an int (which will fail):
```sql
CAST((SELECT example_column FROM example_table) AS int)
```
This might cause an error in which it just straight up hands over the correct value for that particular data we're trying to fetch, and it's short too!

Since `administrator` is way too long of an username, I decided to ditch filtering by `username` and switch to `LIMIT 1` - basically limiting the query result down to one row, default is the first row. Let's see this in action, we will try querying which row does `administrator` lie:
![](/images/viber_image_2026-07-16_10-55-21-171.png)
Thankfully, what we're looking for is on the very first row. In the case it isn't, we can append `OFFSET n` to move down the table one row at a time. Next, we can just query for password and be done with our lab:
![](/images/viber_image_2026-07-16_10-56-02-129.png)