+++
date = '2026-05-04T21:25:16+08:00'
draft = false
title = 'basic-mod1'
tags = ['training', 'CryptoHack', 'writeups', 'cryptography']
description = 'Very basic and straightforward problem, introduction to simple modular arithmetic'
showLastUpdated = true
+++
Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.
```python
encoded = [350, 63, 353, 198, 114, 369, 346, 184, 202, 322, 94, 235, 114, 110, 185, 188, 225, 212, 366, 374, 261, 213]
plain = ""

for n in encoded:
    n = n % 37
    if 0 <= n <= 25:
        plain += chr(n + 65)
    elif 26 <= n <= 35:
        plain += chr(n - 26 + 48)
    else:
        plain += '_'
        
print(plain)
```