+++
date = '2026-05-27T16:40:16+07:00'
draft = false
title = 'Bandit'
tags = ['training', 'OverTheWire', 'writeups', 'Linux']
description = 'Writeups for Bandits'
showLastUpdated = true
+++
# Bandit

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
