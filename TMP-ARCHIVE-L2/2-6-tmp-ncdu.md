# Linux Administration and Networking Basics (Level 2) <br> Լինուքսի կառավարում և ցանցային հիմունքներ (փուլ 2)


## Detect Linux Distribution

There are several ways to understand which distribution are you on.


```bash
cat /etc/os-release
```

```bash
cat /etc/issue
```

```bash
cat /etc/system-release
```
(RedHat/CentOS/Rocky versions **in addition** will have (in form of one symlink of other):
* `/etc/redhat-release`
* `/etc/centos-release`
* `/etc/rocky-release`



### NSDU (NCurses Disk Usage) - better solution

`ncdu` - is better solution to understand current disk space usage.

Install it.

```bash
yum -y install ncdu
```

How it works:
You should specify initial directory (if not specified it uses current one).
Program starts counting disk space inside that directory.

> NOTE! In easc session you will be able to work only inside that dir. 
> To go upper you need to quit and run program again with other directory path.


Example:

```bash
ncdu /var/log
```


Press `?` to see Help screen.

Here are interesting keys:
* `d` to delete file or directory (be careful !!!).
* `g` press multiple times to see usage percentage in different ways.
* `t` show directories on the top (before files)
* `s` sort by size 
* `n` sort by name
* `r` recalculate disk space
* `b` run sub-shell in current directory


