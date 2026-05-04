+++
date = '2026-05-04T21:36:16+08:00'
draft = false
title = 'basic-mod2'
tags = ['training', 'CryptoHack', 'writeups', 'cryptography']
description = 'Similar to basic-mod1 with a tweak'
showLastUpdated = true
+++
Take each number mod 41 and find its modular inverse using Fermat's little theorem, then map it to the following character set: 1-26 is the alphabet (uppercase), 27-36 are the decimal digits, else an underscore.
```python
cipher = [432, 331, 192, 108, 180, 50, 231, 188, 105, 51, 364, 168, 344, 195, 297, 342, 292, 198, 448, 62, 236, 342, 63]
plain = ""

for n in cipher:
    n = n % 41
    n = pow(n, 41 - 2, 41)
    if 1 <= n <= 26:
        plain += chr(n - 1 + 65)
    elif 27 <= n <= 36:
        plain += chr(n - 27 + 48)
    else:
        plain += '_'
        
print(plain)
```