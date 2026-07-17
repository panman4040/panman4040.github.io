+++
date = '2026-07-15T13:01:16+08:00'
draft = false
title = "SQL injection attack, listing the database contents on non-Oracle databases | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++

Unlike previous labs where we are given the table names and its columns names already, this time we have to find them ourselves. Thankfully, every database contains what are called **schemas** - metadata about every other database. On non-Oracle databases, it's usually `information_schema.tables`.

But before extracting data, we will need to [find out the number of columns in the filter table](/training/portswigger/sqli/sqli_practitioner1). In this case, there are two columns:
![](/images/viber_image_2026-07-15_13-49-40-306.png)

Since injection is possible, one may think to query for everything inside the schema right? But since we are only given two columns to work with, and the schema table *may contain* more than two, it's not possible to extract every data using `UNION SELECT` this way:
![](/images/viber_image_2026-07-15_13-50-22-363.png)

So naturally, we should select only two necessary columns from the schema. The first is obvious which is `table_name`, while the second I've opted for `table_type`. Basically it is the classification of the table, the most common values being:
- `BASE TABLE`: normal table
- `VIEW`: not rlly a table, more like a predefined query for other tables
- `SYSTEM VIEW`: internal database views, good for recon

Since our target is the table containing credentials, filtering result by `BASE TABLE` is our safest bet here:
![](/images/viber_image_2026-07-15_13-56-19-217.png)
Surely enough, there is an interesting table called `users_ahgzhv` - most likely containing plaintext credentials. Next, we have to find out its columns names to know what we have to extract data from:
![](/images/viber_image_2026-07-15_13-57-21-047.png)
![](/images/viber_image_2026-07-15_13-57-59-254.png)

