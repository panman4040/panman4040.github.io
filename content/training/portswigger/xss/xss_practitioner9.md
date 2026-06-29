+++
date = '2026-06-29T09:11:16+08:00'
draft = false
title = 'Reflected XSS into a JavaScript string with single quote and backslash escaped'
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
We first try to insert our canary string into the search form on the main page, and view the result in DOM Invader
![](/images/acbc3a70-7aa5-420f-bd42-74cd0a1e85b5.jpg)

As we can see, the canary falls into a `document.write` sink. Upon closer inspection of the source code, we can discover this JS snippet:
![](/images/07e91fab-3fe6-4d53-a5d9-177a1650366f.jpg)

Notice that the single quote is escaped. Double quotes and angle brackets are URL encoded, evident on the query string, before being written into the HTML. But whatever input we type will be assigned to the variable `searchTerms` first, without any encoding save for single quotes.

Using this information, perhaps we can escape the `<script>` tag somehow, and inject our own loaded HTML to trigger XSS? We can test this by attempting to input `<script></script>` into the search bar and see what comes:
![](/images/965af23f-a4c6-4513-9c34-c03df934e34b.jpg)

As expected, the tags are counted as valid HTML. Thus we can craft the following payload:
```html
</script><img src=1 onerror=alert(1)>
```
The HTML parser will see `</script>`, immediately closes the script box regardless of still being inside quotes or not. Then our payload will be triggered. Simple yet informative!