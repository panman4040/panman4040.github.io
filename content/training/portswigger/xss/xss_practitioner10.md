+++
date = '2026-06-29T09:45:16+08:00'
draft = false
title = 'Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
As usual, we first try to insert our canary string along with special characters to see what sticks:
![](/images/0a5de1db-44bb-4add-910a-a29d0b5b7f1d.jpg)

Notice all of the characters are encoded, save for *one trailing backslash* at the end. Since single quotes are escaped, we should expect the same for backslashes and see *two of them* rather than one. This means backslashes are not escaped, and we can use this to our advantage.

We can insert our own backslash character before a single quote to neutralize the backslash that is added by the application.

This means, suppose this input:
```
';alert(document.domain)//
```

is converted to:
```
\';alert(document.domain)//
```

We can use this alternative payload:
```
\';alert(document.domain)//
```

that gets converted to:
```
\\';alert(document.domain)//
```

Here, the first backslash means that the second backslash is interpreted literally, and not as a special character. This means that the quote is now interpreted as a string terminator, and so the attack succeeds.