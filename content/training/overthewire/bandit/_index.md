+++
date = '2026-10-11T16:25:16+08:00'
draft = false
title = 'Bandit'
tags = ['training', 'OverTheWire', 'writeups', 'Linux']
description = 'Writeups for Bandits'
showLastUpdated = true
+++
# Bandit

## Foreword
I really had fun solving these rooms, learnt a bunch more stuff I previously thought was intimidating. Highly suggest yall trying to solve these on your own without AI (trust me the dopamine hits are the real deal). Note that all of these passwords are expired, they are for my personal notes only.

Have fun!

## Bandit 0
Pretty straightforward, log into the game with SSH per below
```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```
After that, we can simply use `cat readme` to get our password

Password: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

## Bandit 1
Using `-` as an argument refers to **STDIN/STDOUT**. So we have to do the following:
```bash
cat ./-
```

Password: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

## Bandit 2
Pretty straightforward, can use TAB to autocomplete the query.
```bash
cat ./--spaces\ in\ this\ filename--
```
Notice how the spaces are separated with `\`

Password: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

## Bandit 3
Can use `ls -a` to view all hidden files
```bash
ls -a
cat ...Hiding-From-You
```

Password: `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

## Bandit 4
Since there is only one human-readable files, can use the following to take a peek:
```bash
head ./-file0x
```
where x ranges from 0 to 9. This will view the first 10 lines by default to avoid terminal cluterring.

The bruteforce approach above works, but becomes tedious when there are a large number of files. We shall use `find` instead:
```bash
find . -readable
```

Password: `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

## Bandit 5
```bash
cd inhere
find . -type f -readable -size 1033c ! -executable
```
The `c` in `1033c` stands for **characters**. If you leave it off, it will look for 1033 512-byte blocks instead! 

`-type f` flag makes sure we are looking for standard files and ignores directories

Password: `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

## Bandit 6
```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```
In Linux, there are three standard streams:
- 0 = Standard Input (`stdin`): This is how data goes into the program. By default, this is linked to our keyboards
- 1 = Standard Output (`stdout`): This is where the program sends its successful results. By default, this prints to our terminal
- 2 = Standard Error (`stderr`): This is a separate stream for error messages. By default, this also prints to our terminal

So `2>/dev/null` means that we redirect the Standard Error stream to `/dev/null` and leave only the successful output.

Password: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

## Bandit 7
```bash
grep millionth data.txt
```
This will show the line where the word "millonth" is on, along with the password

Password: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

## Bandit 8
For this, we shall use the `uniq` command which is used to filter out or report repeated lines in a text file or standard output. But it only detects **adjacent duplicate lines** rather than scanning the whole file.

To amend this, we usually use piping in tandem with `sort`
```bash
sort data.txt | uniq -u
```
where the `-u` flag is used to display unique lines only

Password: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

## Bandit 9
We will use `strings` to first filter all human-readable lines, that is any sequence of characters that has **at least 4 characters long** and ends with `\0`. Then, we pipe the output to `grep '==='` to obtain our password.
```bash
strings data.txt | grep '==='
```

Password: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

## Bandit 10
One quirk of Base64 is that there are usually some padding equal signs at the end of the encoded string
```bash
cat data.txt | base64 -d
```

Password: `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

## Bandit 11
Simple ROT13 decode with `tr`. The syntax is essentially:
```bash
tr 'set1' 'set2'
```
It takes characters from `set1` and maps them directly to the corresponding characters in `set2`.

This is my initial solution:
```bash
cat data.txt | tr 'N-ZA-Mn-za-m' 'A-MN-Za-mn-z'
```
While this works, this falls into a funny case of **Useless Use of `cat`**. Basically it's when `cat` is used to read a single file to pass its contents into a pipe for another command. This makes the shell create two separate processes in memory, in this case the two processes are `cat` and `tr`.

A better way is to redirect the file's content using `<` as per the optimal script below:
```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

Password: `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

## Bandit 12
In this wargame, we are given a **hex dump**. A hex dump is a view of a file where every single byte of data is translated into a hexadecimal number (base-16, using 0-9 and A-F).

When you look at a hex dump, it is usually split into three columns:
- The Offset (Address): Tells you where you are in the file (how many bytes from the beginning).
- The Hex Bytes: The actual raw data, usually grouped in pairs (e.g., 1f 8b 08).
- The ASCII Translation: A text representation of those bytes on the far right. If a byte isn't a printable text character, it usually just shows up as a dot (.).

You can get the original binary file using `xxd -r hexdump.txt output.bin`. After that it's a series of inspecting file format using `file <file-name>` and decompressing them.

Password: `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

## Bandit 13
Pretty tricky wargame introducing SSH and SCP file transfer. First, you will need to transfer the private key to our machine using the following command:
```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private ~/
```
Next, if we try to use that private key to log into bandit14, we will be denied due to the key files "too open". This is why we use SCP to transfer back to our machine to be able to change permission for the file:
```bash
chmod 700 sshkey.private
```
Only then can we log into bandit14 (notice the `-i` flag) and get our password:
```bash
ssh -p 2220 -i ~/sshkey.private bandit14@bandit.labs.overthewire.org
```

Password: `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

## Bandit 14
Connect to localhost through port 30000 using telnet, then type the password obtained and `host:telnet`, then SHIFT + ENTER

Password: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

## Bandit 15
First you need to connect to port 30001 with this:
```bash
openssl s_client -connect localhost:30001
```
where:
- `s_client` is a program inside the OpenSSL toolkit. It stands for SSL/TLS Client and act as a client that knows how to establish a TLS connection with the destination

After that, simply paste the old password and we shall get our password.

Password: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

## Bandit 16
First, we find out which of the ports in the range 31000-32000 has a server listening on them (note: we will use TCP Connect as it can be used even if you're not on `sudoers`):
```bash
nmap -sT -p 31000-32000 localhost
```

One thing to note is that the old password starts with a `k`, which triggers a TLS 1.3 Key Update. OpenSSL will intercept the `k` and send the rest to the server, which is the wrong password. To amend this, simply add the `-quiet` flag that disables all the interactive command (`k`, `r`, `q`):
```bash
openssl s_client -connect localhost:31790 -quiet
```

There should be 5 open ports, we can probe each one with OpenSSL to get our **private key** for the next level.
```bash
chmod 700 /tmp/key
ssh -i /tmp/key bandit17@bandit.labs.overthewire.org
```

## Bandit 17
Simply use `diff` to find which line has been changed, and that's our password.

```bash
diff passwords.new passwords.old
```

Password: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

## Bandit 18
The `.bashrc` file is a hidden configuration script in our home directory `~/.bashrc`. It is essentially the terminal's setup routine. 

Because the `.bashrc` file for the `bandit18` user is scripted to immediately exit once logged in. To circumvent this, we execute the following:
```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org -t "bash --norc"
```
The `-t` flag is what's called a **Force Pseudo-Terminal Allocation**, basically it force opens an interactive keyboard and screen and attach it to the command in the double quotations. In this case our command `bash --norc` starts a brand new Bash shell and ignores the `~/.bashrc` file.

Password: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

## Bandit 19
This is a case of **Privilege Escalation**, basically `bandit20-do` has a special `setuid` access flag that allows users to run an executable (in this case `bandit20-do`) with the file system permissions of the executable's owner (or group, `setgid`). Thus we can act as `bandit20` by running the executable and obtain the password as follow:  
```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

In the real world, if a hacker gains low-level access to a server, they usually search for `setuid` files:
```bash
find / -perm -4000
```
`4000` is the octal representation of the `setuid` bit.

Password: `4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA`

## Bandit 20
Similar to Bandit 19, there is a `setuid` binary called `suconnect`. If we try running it bare, we'll get the following:
```
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. 
If it receives the correct password from the other side, 
the next password is transmitted back.
```
Basically, we need to create a "listener" on some random high port. Whenever an executable listens to that port, the listener sends the old password of Bandit 19 to that executable, in this case it's `suconnect`. `suconnect` verifies the password then yields the password to the next level.

However, creating a listener will hang the terminal, as the listener will keep waiting for a connection that never comes. One simple way to resolve this is to add `&` at the end of the listener command, which will *background the listener* and give us back our shell.

Thus, the solution for this room shall be:
```bash
echo '4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA' | nc -l 54321 &

./suconnect 54321
```

Password: `bW9kBv5WC3P4yoDyf12LSdGuNz5ka6hY`

## Bandit 21
This room introduces `cron`, which is a program that runs other jobs, also known as cron jobs, on a schedule. Each job is one line of text in a config file. On this room those files sit in `/etc/cron.d/`
```
minute  hour  day-of-month  month  day-of-week   command
  *      *         *          *         *         means "every time"
```

Note that `*` means "every". Example:
```
* * * * * root  /usr/bin/some_script.sh
```
This reads as: every minute, as the root user, run that script.

For this particular room, we shall `cd` to `/etc/cron.d` for the configuration:
```
behemoth4_cleanup  cronjob_bandit23  leviathan5_cleanup
clean_tmp          cronjob_bandit24  manpage3_resetpw_job
cronjob_bandit22   e2scrub_all       otw-tmp-dir
```
We can see that there is a `cron` called `cronjob_bandit22`, which directly relates to our next room. Inspecting that cron yields:
```
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
We now know there is a cron job `/usr/bin/cronjob_bandit22.sh` which runs continuously, yet dumps all its output to `/dev/null`. Now we can inspect what commands are being executed inside that cron job:
```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
It basically redirects the output of the password for `bandit22` to a random file in the `/tmp` folder, and changes the permission for that file. Inspecting `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` will give us our password for the next room.

Password: `RYVux2rHEm9tiXHmLFzuR7Vhx6AZQMEz`

## Bandit 22
The workflow is similar to Bandit 21, inspecting `usr/bin/cronjob_bandit23.sh` gives:
```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```
Hashing a text with `md5` yields a **32-character hex string** plus a trailing `-`. Piping that output to `cut -d ' ' -f 1` basically means spliting on space, then takes field 1 which is just the hash (dropping the `-`).

Therefore, we know that the password to the next room lies `tmp` plus whatever `mytarget` is. Running the commands ourselves will yield us our password. Note that `myname`'s value is the user running the job, which is `bandit23`.

Password: `gKXDTAXnIz3OBxiPjRZ2uqutUlPZrBsw`

## Bandit 23
The workflow is similar to Bandit 22, inspecting `usr/bin/cronjob_bandit24.sh` gives:
```bash
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

Since cron runs this as `bandit24`, the code basically jumps into `/var/spool/bandit24/foo`. It then goes through each file (`*` matches all normal files, `.*` matches **hidden** ones), obtain the owner's username for that file (the `-f` flag means it exists and **is a normal file**), then executes the file with a timeout of `60` seconds. `-s 9` means that if it overruns, send signal 9 (`SIGKILL`) which is a forced kill. 

Our job after this should be straightforward right? We need to create a script within `/var/spool/bandit24/foo`, make it read `/etc/bandit_pass/bandit24` on `bandit24`'s behalf. Since we ourselves are `bandit23`, our script is guaranteed to run no matter what, though the cron script will promptly delete our script later.

We can create a dummy folder at `/tmp` since that's where everything has write permission. Just to be sure, we can use `chmod` to modify the permission for that folder so that everyone can write to our directory:
```bash
mkdir /tmp/dummydir
chmod 733 /tmp/dummydir
```

After this, we can ask `bandit24` to redirect whatever inside `/etc/bandit_pass/bandit24` to our output text file that lives in `/tmp/dummydir` as follow:
```bash
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/dummydir/output.txt
```
Note that `bandit24` needs to both **read and execute** our newly added script, therefore we must grant the read *and* execute permission of that script for everyone at minimum.

After saving our script, we wait for the cron job to fire for 60 seconds top, then `cat`-ing our `output.txt` and obtain the password.

Password: `hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv`

## Bandit 24
A daemon is listening on port 30002 and will yield the password for the next room if given a correct 4-digit pincode. Let's first try to connect and see what gives:
```bash
nc localhost 30002
```

```
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
```
The logical way to do this is bruteforcing, one can come up with an automated shell script to do the grunt work for us. Here is my initial script:
```bash
#!/bin/bash

bandit24='hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv'

for i in {0000..9999}
do
        msg="$bandit24 $i"
        echo "$msg" | nc localhost 30002
done
```
This works but it keeps initiating connections for every iteration, which is not that great. Instead, we can generate every line first *then* pipe the whole stream to one connection only:
```bash
#!/bin/bash

bandit24='hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv'

( for i in {0000..9999}; do echo "$bandit24 $i"; done )| nc localhost 30002 > output.txt
```

After obtaining our output file `output.txt`, we can `grep` to **not** include invalid connections. Since every invalid connection contains the string `Wrong!`, we can use the following command:
```bash
grep -v "Wrong!" output.txt
```

```
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is SoHfqMOEqIX2IYKVciZxvgpR9a2Djx4P
```

Password: `SoHfqMOEqIX2IYKVciZxvgpR9a2Djx4P`

## Bandit 25
After transfering the private key for Bandit 26 to our machine, we can attempt to connect as normal like the following:
```bash
ssh -p 2220 -i ~/bandit26.sshkey bandit26@bandit.labs.overthewire.org
```
But we are immediately kicked out. The problem statement also hints that the shell is not `/bin/bash` as usual, so now our task is to find where the shell lies. It is simple honestly, `/etc/passwd` stores user accounts alongside their assigned default shells:
```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
`/usr/bin/showtext` is the shell for Bandit 26. Now we need to understand what `showtext` does and how to escape it:
```bash
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```
The fact that there is `exec` here means that the current shell is replaced with `more`. `more` is a **pager** which shows a file one screenful at a type, and in this case it's showing `~/text.txt`.  But if the screen is *not large enough*, `more` shows one screen then hangs. Thus, we should reduce the window to only a **few rows** high so that it doesn't immediately exit out on us.

Another food for thought is that `more` launches `vi`, and we can switch to `vi` by pressing `v`.

Why do we do this? Within `vi` we can **actually run shell commands**, and even **launch a shell**. Bandit 26 also requires us to obtain the shell to go to Bandit 27, so that's exactly what we are going to do.

We start a command in `vi` by pressing a colon `:`, then whatever command follows and press Enter. To obtain the shell, we can type the following command:
```
:set shell=/bin/bash
:sh
```
After execution, we finally obtain the shell for Bandit 26!

## Bandit 26
Following Bandit 25, `ls` reveals that there is a `setuid` binary `bandit27-do`. Upon executing without arguments, we will get the following:
```bash
./bandit27-do
```

```
Run a command as another user.
  Example: ./bandit27-do id
```

The rest should be trivial:
```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```
Password: `STJLJBRRphMxKB392CT4iOr5CbzPU9ER`

## Bandit 27
```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Password: `y8Yd2ssKcpHpud7UvOSOxwamRMzIGIeQ`

## Bandit 28
```bash
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
```

There is a single `README.md`, which reads:
```
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```

The password for the next room is redacted, but what about previous commits? Maybe someone accidentally pushed the plaintext password before redaction. Let's try inspecting with `git log`:
```
commit 4357a6e76c600eb95daf13b1c315b95faab9d4e6 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Sat Sep 26 21:53:16 2026 +0000

    fix info leak

commit fac3dcdd94f71fb2d3286d6531d4b8523ec64d30
Author: Morla Porla <morla@overthewire.org>
Date:   Sat Sep 26 21:53:16 2026 +0000

    add missing data

commit a0e6303aa9bc3dd65dd3fcbfe752c5a68dee5a94
Author: Ben Dover <noone@overthewire.org>
Date:   Sat Sep 26 21:53:16 2026 +0000

    initial commit of README.md
```


```
diff --git a/README.md b/README.md
index 5c6457b..42331d9 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: xxxxxxxxxx
+- password: Em7eGtqaMySwNFjCpwzzHhLhospOcdt0
```

Password: `Em7eGtqaMySwNFjCpwzzHhLhospOcdt0`

## Bandit 29
Unlike Bandit 28, the local branch does not show anything funny. No password redaction and the sort:
```
diff --git a/README.md b/README.md
index 1af21d3..2da2f39 100644
--- a/README.md
+++ b/README.md
@@ -3,6 +3,6 @@ Some notes for bandit30 of bandit.

 ## credentials

-- username: bandit30
+- username: bandit29
 - password: <no passwords in production!>
```

This means we need to look at a *different branch* for inspection, but only local branches wouldn't suffice:
```bash
git branch
```

```bash
master
```

How about **remote branches**? To do so, we need to add the `-a` flag at `git branch`, `-a` can be understood as "all"
```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

Ah, now there are some interesting remote branches. `origin/dev` seems the most sus here, so let's check it out:
```bash
git checkout origin/dev
```

```
* (HEAD detached at origin/dev)
  master
```

This means that the `HEAD` at `origin/dev` is current **ahead** of that of `master`. One look at the log corroborates this:
```
commit 509387f8f6fb2b5db2cd8e48cfdd0818d39b805d (HEAD, origin/dev)
Author: Morla Porla <morla@overthewire.org>
Date:   Sat Sep 26 21:53:18 2026 +0000

    add data needed for development

commit db476e79b25ef65e1ea4d87891a5766f1fc45ea5
Author: Ben Dover <noone@overthewire.org>
Date:   Sat Sep 26 21:53:18 2026 +0000

    add gif2ascii

commit a8df1c59a139b27fd43c6dbc4d15c715b92b81e5 (origin/master, origin/HEAD, master)
Author: Ben Dover <noone@overthewire.org>
Date:   Sat Sep 26 21:53:18 2026 +0000

    fix username

commit e288349de8c1b298eadfe2a2d5f754a695b4f880
Author: Ben Dover <noone@overthewire.org>
Date:   Sat Sep 26 21:53:18 2026 +0000

    initial commit of README.md
```
See that there are two more commits than the `master` branch. From here it's trivial to obtain our password for the next room:
```bash
git diff a8df1c59a139b27fd43c6dbc4d15c715b92b81e5 509387f8f6fb2b5db2cd8e48cfdd0818d39b805d
```

```
diff --git a/README.md b/README.md
index 1af21d3..d395d04 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for bandit30 of bandit.
 ## credentials

 - username: bandit30
-- password: <no passwords in production!>
+- password: jq9Dfg2rXsfYsWMgFuKlXhphjdH7USgX
```

Password: `jq9Dfg2rXsfYsWMgFuKlXhphjdH7USgX`

## Bandit 30
This room introduces **tags**, basically they are labels to a commit, used for things like release versions.
```bash
git tag
```

```
secret
```

Now there exists a hidden tag called `secret`, one look at the tag using `git show secret` yields our password.

Password: `82NkymblpGBYmIXG6ZQ8YldBYstHpfUf`

## Bandit 31
One look at the `README.md` drops some hints for us:
```
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```

This is basically the `git add`, `git commit`, and `git push` workflow. First, simply create the `key.txt` satisfying the requirements.
```bash
nano key.txt
```

Then add the following lines:
```bash
# Stage the change
git add key.txt

# Commit the change
git commit -m 'some message'

# Push the change to the remote repo
git push
```

However, there's a catch. `key.txt` is explicitly mentioned in `.gitignore`, we amend this by adding an `-f` flag to "force" the staging. You can also verify whether staging is successful or not by typing `git status`. Here is an example of a successful staging attempt:
```
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   key.txt
```
After successfully pushing our file, we shall obtain our password.

Password: `pWuj5jBQ6IgV0NXwiH6g1pXRF8S1YvbT`

## Bandit 32
This is the final room for Bandit, and it's quite a final boss I would say.

Once you connect to the room successfully, there is a peculiar line:
```
WELCOME TO THE UPPERCASE SHELL
```

You should notice by now that whatever typed into the shell, whether they're legit commands or gibberish, is rejected:
```
>> ls
sh: 1: LS: Permission denied
>> sh
sh: 1: SH: Permission denied
>> cat
sh: 1: CAT: Permission denied
>> sklafda
sh: 1: SKLAFDA: Permission denied
>> kasdfjksdafkjsdf
sh: 1: KASDFJKSDAFKJSDF: Permission denied
```
Notice that this shell is said to be "UPPERCASE", and it tries to run **uppercase** version of whatever we typed (example: `ls` turns into `LS`). This means we need to find a way to "break" this rule somehow. The room does hint using `man` and `sh`, so we are trying just that.

One look at the manual for `sh` shows some interesting parameters:
```
$            Expands  to  the  process ID of the invoked shell.  A subshell retains the same value of $ as its
                    parent.


0 (Zero.)    Expands to the name of the shell or shell script.
```
We can infer that running `$0` expands to the name of the current shell. In layman's terms, that's basically replacing this current shell, the uppercase version, with another copy of the shell. One more thing is that uppercasing `$0` literally means naught, it stays the same. Let's try doing just that:
```
>> $0
$
```
We should be able to execute normal commands from here on out, thus we can obtain the password trivially:
```bash
$ cat /etc/bandit_pass/bandit33
```

Password: `u4P2CyPOwPGLe94RdD9Uo2FxFwvnFswM`

## Bandit 33
Nothing here yet. But we can verify the answer in Bandit 32 by logging in. Getting the content of the last `README.md`
![](/images/otw.png)

It has been quite a journey! Worth the 2 days of ditching assignments