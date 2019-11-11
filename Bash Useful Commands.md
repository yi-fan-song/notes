---
tags: [Bash, Linux, Notebooks/Deployment Goodies, Notebooks/Documentation]
title: Bash Useful Commands
created: '2019-11-11T02:15:31.486Z'
modified: '2019-11-11T04:02:05.052Z'
---

# Bash Useful Commands

This file is for useful bash shell useful commands I find. Commands might difer for windows powershell, have a look at [microsoft docs](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-6)

## Transfer file over ssh

Using scp:
```console
scp {source} {destination}

scp path/to/file user@recipient:/path/to/dest
```
[stack exchange](https://unix.stackexchange.com/questions/106480/how-to-copy-files-from-one-machine-to-another-using-ssh)

## Download a file

Use wget
```console
wget {url}

wget https://www.example.com/this/file
```

## Renaming/moving files

Use mv
```console
mv {file(s)} {destination}

mv /path/to/file file /path/to/dest/
```
