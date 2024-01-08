# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)



##  List Open Files (lsof)


**lsof** will list information about the files that are opened by various processes. 
Remember that in Linux/Unix, everything is a file ( pipes, sockets, directories, devices, etc.). 
So by using **lsof**, you can get information about any opened (currently used) files.


Simply typing **lsof** will provide a list of all open files belonging to all active processes. 

Most columns are self explanatory.
**FD** & **TYPE** might need explanation
```bash
FD – file descriptor number or type. Some of the values of FDs are,
◾cwd – Current Working Directory
◾pd – Parent Directory
◾rtd – Root Directory
◾txt – Text file
◾mem – File loaded in memory (mostly a shared library)
◾mmap – Memory mapped device
◾NUMBER – Represent the actual file descriptor. character after the number represents the mode in which the file is opened
	r - read, w- write, u - read and write


TYPE – type of the file:
◾ REG – Regular File
◾ DIR – Directory
◾ LINK – Symlink
◾ INET – network connection socket
◾ CHR – Character special file

(see `man lsof` for more info)
```

### PRACTICE

Examples
```bash
lsof -r 3 /var/log/syslog /bin/bash show opened files each 3 sec 
lsof +D /var/log/		opened files under a directory
lsof -c ssh -c rsyslog	list opened files based on process name
lsof /home 	    list processes using a mount point
lsof -u student 		list files opened by a specific user 
lsof -t /tmp/f1		list PIDs that opened a particular file
lsof -t -u student		output only PIDs of the processes
lsof -p 1753 -R		list all open files by process & show PPID
lsof -I			list all network connections 
lsof -i | grep ssh 
lsof -i -a -c ssh  	 list the network files opened by the processes starting with ssh
lsof -i :22	 processes listening a particular port
lsof -i tcp; lsof -i udp list all the TCP or UDP connections 
lsof -i @192.168.1.100 list active connections from some IP 
lsof -i tcp@192.168.1.100:22 combine the above
```

