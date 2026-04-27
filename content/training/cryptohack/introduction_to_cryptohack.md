+++
date = '2026-04-27T07:23:16+07:00'
draft = false
title = 'Introduction to CryptoHack'
description = 'Important remarks for the introductory challenges'
showLastUpdated = true
+++
# Introduction to CryptoHack

Since this is an introduction, these are pretty straightforward stuffs. But there are a few notes to consider:

## Base64
This allows us to represent binary data as an ASCII string using an alphabet of 64 characters. One character of a Base64 string encodes 6 binary digits (bits), and so 4 characters of Base64 encode three 8-bit bytes.

There is also padding introduced when the number of bits is not divisible by 6. Read more here: https://en.wikipedia.org/wiki/Base64#Examples

## Bytes and Big Integers
For this problem, I had to set up Ubuntu on WSL for ease of working with PyCryptodome and possibly other future libraries (this took a lot of time to set up kms) 

The problem itself is quite straightforward, it introduces how to convert characters to numbers for cryptosystems such as RSA. The most common way is to take the ordinal bytes of the message, convert them into hexadecimal, and concatenate. This can be interpreted as a base-16/hexadecimal number, and also represented in base-10/decimal.

```python
message: HELLO
ascii bytes: [72, 69, 76, 76, 79]
hex bytes: [0x48, 0x45, 0x4c, 0x4c, 0x4f]
base-16: 0x48454c4c4f
base-10: 310400273487
```

## XOR
The course introduces XOR, its properties, and how it can be used to encode/decode data. I had a fun time revising my mental digital logic for these problems.

https://cryptohack.org/courses/intro/xorkey1/

This is arguably the most difficult problem in this section in my opinion, requires keen intuition (maybe because it's worth 30 points...)

## Some Python quirks
One pattern I noticed is that we always convert hex to bytes using `bytes.fromhex(hex)`. In Python, a byte is just an array of integers from 0 to 255. 

However, if a byte's value falls between 32 and 126 (ASCII range), Python shows the actual character (e.g. `a`, `B`, `!`). If the value is outside that range, Python shows the hexadecimal value using the escape sequence `\xHH` (e.g. `\x3f` is `0x3f`)

Converting to bytes is generally considered good practice for cybersecurity

```python
# A mix of printable and non-printable bytes
my_bytes = bytes([65, 0, 33, 255])
print(my_bytes)
# Output: b'A\x00!\xff'
```
