+++
date = '2026-05-25T11:25:25+07:00'
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