---
title: "File Modes and Permissions"
description: "Linux におけるファイルの権限についてのメモ"
date: 2021-02-03
tags: ["Linux"]
---

Example result of `$ ls -l`
```sh
total 16
-rw-rw-r-- 1 popondayu popondayu 6811 Jan 29 14:07 big_picture.md
-rw-rw-r-- 1 popondayu popondayu   61 Feb  1 12:14 file_modes_and_permissions.md
-rw-rw-r-- 1 popondayu popondayu 2359 Feb  1 12:11 manipulating_process_memo.md
```

## How to read `ls -l`
```
0123456789		user			group			
-rw-rw-r-- 1 popondayu popondayu 6811 Jan 29 14:07 big_picture.md

0: file type
123: user permissions
456: group permissions
789: other permissions
```

#### [0] The first character of the mode is the **file type**. 
* `-` denotes a **regular** file, nothing special about the file
* `d` indicates **directory**
other types later...

#### [123, 456, 789] the rest of a file's mode contains the permissions.
* `r` Means that the file is **readable**
* `w` Means that the file is **writable**
* `x` Means that the file is **executable** (you can run it as a program).
* `-` Means nothing

permissions
* 123 = **user permissions**: pertain to the user who **owns** the file
* 456 = **group permissions**: are for the file's group
* 789 = **other permissions**: are for everyone else on the system

Some **executable** files have an `s` in the user permissions listing instead of an `x`. This indicates that the excutable is **setuid**, meaning that when you execute the program, it runs as though the file owner is the user instead of you. Many programs use this setuid bit to run as **root** in order to get the privileges they need to change **system files**.

## Modifying Permissions
`chmod` (change mode) command to **change permissions**. First, pick the set of permissions that want to change, and then pick the bit to change. 

Example of `chmod` (**absolute** change):
```sh
$ chmod 644 file
```
Each number represents each permissions.  
Digits select permissions for the user, group, and others: read (**4**), write (**2**), execute (**1**)

For example:
```
7 = 4 + 2 + 1 = r + w + x
5 = 4 + 1 = r + x
4 = r
2 = w
1 = x
```

## Symbolic Links

A **symbolic link** is a file that points to another file or a directory, effectively creating an alias. 

```
lrwxrwxrwx 1 ruser users 11 Feb 27 13:52 somedir -> /home/origdir
```
`l` stands for symbolic link

#### Creating Symbolic Links
To create a symbolic link from `target` to `linkname`, use `ls -s`
```
$ ln -s target linkname
```


## Reference
* `$ man chmod`
* How Linux Works
