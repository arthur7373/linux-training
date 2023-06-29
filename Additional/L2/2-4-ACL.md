# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Access Control Lists (ACL) 
	
With ACL (Access Control Lists) it's possible to set access permission to files or directories more strictly than standard permissions.

In recent Linux versions acl package is already installed, but in case of need it can be installed with:

`apt -y install acl`

or

`yum -y install acl`

In order to use ACL, it should be enabled as a mount option for that mounted filesystem/partition. 
Most probably it is already enabled by default. To check you can use following:
(in case device name is different use `df -hT` or `lsblk` to find exact name)

`tune2fs -l /dev/sda1 | grep "Default mount options"`

If **acl** is present in default mount options then you can start using it.

Create test directory 
```bash
mkdir /opt/acltest
```

Assign acl to student
```bash
setfacl -m u:student:rwx /opt/acltest
```

Check

`getfacl /opt/acltest`

Now switch to user **student** and try creating something inside that dir.
(you should notice additional "**+**" sign after permissions)
```bash
su - student
mkdir /opt/acltest/mydir
ls -la /opt
ls -la /opt/acltest
touch /opt/acltest/myfile
ls -la /opt/acltest
```



More info at:

https://www.server-world.info/en/note?os=Ubuntu_22.04&p=acl
