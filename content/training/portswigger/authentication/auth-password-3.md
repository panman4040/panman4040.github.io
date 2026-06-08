+++
date = '2026-06-08T15:31:16+08:00'
draft = false
title = 'Broken brute-force protection, IP block'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = 'Bypass IP block while brute forcing II, with Python script'
showLastUpdated = true
+++
The key to this lab is to occasionally insert our valid credentials inbetween the enumerations, so that *maybe* the rate limit imposed on our IP is reset to zero. This is never guaranteed for every web app.

To solve this, we can either use the Turbo Intruder extension or macros. But for the sake of simplicity, we need only edit the username and password payloads so that our valid credential and our brute-forcing attempts alternate. The password wordlist can be done through a simple Python script as follow:

```python
def main():
    # Create new wordlist
    new_f = open("new_wordlist.txt", "w")
    
    with open("wordlist.txt", "r") as old_f:
        for line in old_f:
            new_f.write(f"peter\n{line}")

if __name__ == "__main__":
    main()
```

![](/images/5a6e0158-132f-4924-a7cc-bdd4651fa81a.jpg)






