+++
date = '2026-10-16T21:27:00+08:00'
draft = false
title = 'SCC Finals 2026'
description = ''
tags = ["ctf", "writeups", "scc"]
showLastUpdated = true
+++
# Forewords
Very well-made challenges, if only I were better at CTF, I could appreciate their efforts more

I mostly solved web challenges (as it is my forte) for this final. But I forgor to take screenshots while solving those challenges, and all the s3 buckets are closed. So I cannot post a proper writeup otherwise people will call me larping 😭

There were 6 web challenges, they all require you to know pretty advanced stuff. Things like SSTI, race conditions, SSRF, and HTTP request smuggling. Oh and also one warm-up question about an exposed `/.git`.

There is actually a warmup misc question worth 200 points I did actually capture though:

# AOL 
![](/images/aol1.png)

Basically the gist is that we had an argument with some friend a while back, and that friend holds our flag of which we need. You can probably piece that having an argument = blocking, and coincidentally there is only 1 singular person in our friend list which we blocked - `kelseym84`.

So I unblocked her and she just straight up spewed out some poem about something:

![](/images/aol2.jpg)

To put it simply, she says that there is *another* user that is holding the flag, and the poem is the hint for her username. 

```
a girl got a gift with a lid and a warning
say what she was handed. all one word, no apostrophe
and it rhymes... with your twenty years of blocks
```

Sounds familiar?
![](/images/Lawrence_Alma-Tadema_10.jpeg)

Pandora's Box. No apostrophe. pandorasbox. Rhymes with "blocks".

Upon messaging her, she will indeed give us the flag we need:
![](/images/aol3.png)

Flag: `tim3_i5_@_th1ef`

# Archive
There is actually another "forensics" challenge I managed to capture. Basically there is a file `ARCHIVE.IMA` that details a dossier of some program (maybe it's part of the *lore*), scrolling down gives:
```
...
not been revisited.

4. DISTRIBUTION

Copy 1 is held in the cabinet. Copy 2 is this working copy.

SENTINEL DIRECTORATE                          PAGE 3 OF 3
CONTINUITY Program, CLEARANCE DOSSIER
DOSSIER SERIAL 7714/C                     COPY 2 OF 2 (WORKING)

5. CLEARANCE

CLEARANCE: flag{4rch1v3_49ef1a2978}

6. AUTHORITY

Issued under the continuity program. Valid for the current period.
Countersigned R. MARSDEN, COMMS SECURITY.
...
```
Yeah the flag is literally in the open like that. I don't know what the organisers were going with this, but hey nobody says no to 200 points.
