+++
date = '2026-05-04T21:50:16+08:00'
draft = false
title = 'rotation'
tags = ['training', 'CryptoHack', 'writeups', 'cryptography']
description = 'Simple implementation of Caesar cipher'
showLastUpdated = true
+++
First, calculate the shift amount. Then for each alphabetical character in the ciphertext, subtract that shift amount with the current character, rotate back to z/Z if went overboard
```python
cipher = "xqkwKBN{z0bib1wv_l3kzgxb3l_555957n3}"
diff = ord(cipher[0]) - ord('p')
plain = ""

for c in cipher:
    ch = ord(c) - diff
            
    if c.islower():
        if chr(ch).islower():
            plain += chr(ch)
        else:
            plain += chr(ch + 26)
            
    elif c.isupper():
        if chr(ch).isupper():
            plain += chr(ch)
        else:
            plain += chr(ch + 26)
            
    else:
        plain += c
    
print(plain) 
```
Note that this Python implemention is not exactly the prettiest in the world. And I struggled quite a bit because I thought digits are also rotated (its not lol)