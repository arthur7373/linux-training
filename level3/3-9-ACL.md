# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## ACL (Access Control Lists)

### Check if ACL is present

Linux filesystem permissions are today more than we might generally know.

Basically we know 
* **R**ead 
* **W**rite 
* e**X**ecute 

permissions for:

* **U**ser (or user owner)
* **G**roup (or owner group)
* **O**ther (everyone else)

Plus three other attributes SUID, SGID, Sticky Bit.

However any modern Linux has now possibility to use additional ACLs (Access Control Lists), 
which enable to manage more fine-grained control of who has access to what information.

ACLs enable to set **additional** permissions for file/directory to individual users or groups
without changing the base ownership and permissions.

ACL is Linux kernel feature and is currently supported by many modern filesystems, including (but not limited to):
* EXT2, EXT3, EXT4 
* XFS <br>
...


###  Prepare test environment

As `root` create a set of directories and files:

(there may be need to install `tree` command with `yum -y install tree`)

```bash
sudo mkdir -p /opt/project{1/dir{3,5},2/dir{4,6}} ;\
sudo touch /opt/project{1/file{7,9},2/file8} ;\
tree -fpug /opt/project*

```

### List current ACL with `getfacl`

Check initial ACL list

```bash
getfacl /opt/project*/* | less
```

As we can see no special permissions exist initially


### Create additional ACL with `setfacl`

Try modifying some file <br> 
**as user `student`**

You should not be able to do so.

```bash
echo "Test" > /opt/project1/file7
```

```bash
setfacl -m u:student:w /opt/project1/file7 ;\
getfacl /opt/project1/file7

```

Notice added line `user:student:-w-`

Also notice we can see additional ACL are present with `ls -l` too:

```bash
ll /opt/project1/file7
```

NOTE: **[+] is added on attribute**

Now try again  <br> 
**as user `student`**, <br>
it should work now:

```bash
echo "Test1" > /opt/project1/file7 ;\
echo "Test2" >> /opt/project1/file7 ;\

```

But this will no work for other file:

```bash
echo "Test" > /opt/project1/file9
```

<br><br>

At the same time if you try to see/read that file it will not work

```bash
cat /opt/project1/file7
```

Why ?

From the above `getfacl` output you may notice:

> `user:student:-w-` <br>
> ... <br>
> `other::r--`

We see that even though all others have **r**, student is specifically  mentioned
and thus should be added separately

Let's fix that

We can remove all ACLs
```bash
setfacl -b /opt/project1/file7
```

And set new ones <br> (previous removing was not necessary for this, just used to learn `-b' option)

```bash
setfacl -m u:student:rw /opt/project1/file7 ;\
getfacl /opt/project1/file7

```

Now try again  <br> 
**as user `student`**, <br>
it should work now:

```bash
cat /opt/project1/file7
```

### Mask

You may notice `mask` option in output of `getfacl`, what is it for?
(don't mix with `umask`, which is totally different thing).

Mask acts as additional/final global permission filter.
So for all ACLs (mask is not touching primary rwx permissions) real/effective permissions
will be what is allowed by mask.

I case we don't specify the mask specifically it will be flexible to allow everything we specified for our ACLs.
But if we will specify that, we might limit some access.

Currently we see from `mask::rw-`

```bash
getfacl /opt/project1/file7
```

But let's limit it manually

```bash
setfacl -m m::r /opt/project1/file7 ;\
getfacl /opt/project1/file7

```

> NOTICE  what added at the end of the line <br>
> `user:student:rw-		**#effective:r--**` <br>
> it means that real permissions of student are now only `r`

Let's check if it is so.
As user **student** run the following:

```bash
echo "Test3" >> /opt/project1/file7 ;\

```

You should get `Permission denied`.

Now let's fix the mask:

```bash
setfacl -m m::rw /opt/project1/file7 ;\
getfacl /opt/project1/file7

```

And try again as user **student** run the following:

```bash
echo "Test3" >> /opt/project1/file7 ;\

```


### ACLs for directories

Set ACL to a directory recursively (**-R**).

> NOTICE we work with second directory `/opt/project2`

```bash
setfacl -R -m u:student:rwx /opt/project2 ;\
getfacl /opt/project2

```

And now try as user **student** run the following:

```bash
mkdir /opt/project2/dirNEW ;\
echo "TEST NEW" > /opt/project2/fileNEW
```

Check

```bash
tree -fpug /opt/project2
```

As we learned there is much more that basic `rwx` permissions in modern Linuxes !

More info

* https://www.server-world.info/en/note?os=CentOS_8&p=acl
* https://www.redhat.com/sysadmin/linux-access-control-lists
* https://linuxdatahub.com/masks-in-acl-linux-explained-with-examples-access-control-lists-mask/
* https://www.kryukov.biz/soderzhanie/sistema-bezopasnosti/posix-acl/setfacl/
