+++
date = '2026-06-25T09:31:16+08:00'
draft = false
title = 'Reflected DOM XSS'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
As per the usual, when we try inserting our canary into the search functionality, the result falls into the `eval` sink which is *very dangerous*.
![](/images/45abd775-613f-4bd9-b035-cde16bc4a436.jpg)

Upon closer inspection of the stack trace, we find out the function that manages sending our search requests. The developer here did not use `JSON.parse()` safely but dump the raw response text from the server directly into `eval`. This opens up opportunities for XSS.
![](/images/fad6b6e5-025b-4451-924a-7fe7109bb46e.jpg)

To exploit this, we should think of a way to escape this JSON string. One might try to append `"}`, but `"` is safely encoded as `\"` so it won't work. However, we can add another backslash, `\\"`, the two backslashes in a row will confuse the parser and interpret this as an attempt to print a literal backslash, completely ignoring `"`!

After playing with other characters, we should find out that `/` is not being escaped by a backslash. `//` is mandatory in *almost all* DOM XSS injection attacks as it comments out whatever garbage is left behind our payload. We can then craft the following payload:
```
\"}; alert(1);//
```
Then the expression inside the `eval` sink will become:
```javascript
var searchResultsObj = {"results":[],"searchTerm":"\\"}; alert(1);//"}`
```
