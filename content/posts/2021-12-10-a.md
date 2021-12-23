---
title: Let's Discuss STDIN, STDOUT and STDERR
date: 2021-12-10 13:42:00
tags:
    - bash
categories:
    - tech
keywords:
    - bash
    - stdin
    - stdout
    - stderr
---

### So what are stdin, stdout and stderr in linux or unix bash?
Whenever we run a command in terminal the terminal creates three data stream one for standard input, one for standard output and one for standard error.

STDIN : As said previously stdin is responsible for taking input in terminal. We use read command to take user input from keyboard. This read command is using stdin stream.

```bash
read
```


STDOUT : When we run a command in the terminal, the output(not error output) is sent to the stdout and terminal prints it.

```bash
ls
```


STDERR : STDERR is the stream where the error from a command is sent.

```bash
acommand
```

In linux all the streams are taken as virtual files and like real files we can read from it or write in it. And to uniquely identify each of this virtual files a file descriptor number is assigned to each of them. The file descriptor for this three streams are given below:

STDIN -> 0

STDOUT -> 1

STDERR -> 2

So how can we read from this streams specifically? For example if we want to run a command and read from the may be, stderr stream and stdout stream separately.

We have discussed the file descriptor of these streams already.

If we want to write in a file from the stderr stream we simply use this command

```bash
anything 2> err.txt
```
we can also read from multiple stream and write to multiple file

```bash
anything 2> err.txt 1> output.txt
```