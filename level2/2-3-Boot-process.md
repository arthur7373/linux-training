# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Managing Boot Process (SystemD)

Booting a Linux operating system on some device involves a sequence of events to complete startup:
1. **BIOS / POST**
2. **Bootloader**
3. **Kernel**
4. **System Initialization** ( **INIT** / **SystemD** )


1. **BIOS / POST**

When the device is powered on instructions stored in its firmware (called BIOS - Basic Input/Output System)
are first processed. BIOS performs Power On Self Test (POST) to 
ensure **hardware functionality** (eg: RAM is available). After passing POST, 
the BIOS searches through its **disk boot order**, loads, and executes 
the small intermediate program called bootloader.

2. **Bootloader**

Bootloader role is to **load the operating system**. 
Most widely used Linux bootloader is the **GRUB2** (GNU GRand Unified Bootloader). 
GRUB may display splash screen with menu to select Linux kernel to boot. 
The splash screen will wait a few seconds to select and option. 
At this time boot process can be interrupted to enter single user mode (also known as rescue mode), 
used for recovery tasks such as resetting passwords.
In case of no input from keyboard bootloader will load the default kernel into RAM.
Thus Linux kernel becomes first running proccess (with ID **0**).

3. **Kernel**

Once the Linux kernel starts it checks hardware from operating system point of view (enable needed drivers, etc.) and
then starts first process (process ID **1**) with the responsibility of doing the rest 
(starting services and processes).

5. **System Initialization** ( **INIT** / **SystemD** )
After the bootloader starts the kernel, the kernel starts the first 
process which initializes the rest of the system, and runs until the system is shut down.  

Linux system initialization for a long time was handled by the _Unix-inspired SystemV_ **init** 
process, which ran scripts to start services in a defined and configurable order to reach a 
series of states, called **runlevels**. 

The **Upstart** initialization system was developed as a replacement to _SystemV_ that would 
trigger actions based on events, rather than running scripts in a particular order. **Upstart** was 
backwards compatible with _SystemV_ runlevels, and ran _SystemV_ init scripts.
 
Current most popular initialization system for Linux is  **Systemd**. It is more modular, performant, integrated. 
**Systemd** uses **targets** instead of **runlevels**. 

By default, there are two main targets:
**multi-user.target** - analogous to **runlevel 3**
**graphical.target** - analogous to **runlevel 5**

**Systemd** also can run old _SystemV_ **init** scripts. 

Goal of **targets** or **runlevels** is to process system initialization and bring 
the Linux system to specific state.

Once the Linux kernel has booted, it starts the first initialization process: **init** / **Upstart** / **SystemD**. 

This process: 
- manage the startup process
- manage the services running (enable/disable, start/stop)
- shutting the system down

SystemV Runlevel	Systemd equivalent	Description

0 (HALT)	poweroff.target  	Shuts down the system.

1 (SINGLE-USER MODE)
	rescue.target	Mode for administrative and 
system rescue tasks. Only the 
root user can log in.

2 (MULTI-USER MODE)
		All users can log in, but network 
interfaces aren’t configured 
and networks services are not 
exported. Display manager is not 
started.

3 (MULTI-USER MODE WITH 
NETWORKING)
	multi-user.target	Starts the system normally. 
Display manager is not started.

5 (START THE SYSTEM NOR-
MALLY WITH APPROPRIATE 
DISPLAY MANAGER (WITH 
GUI))
	graphical.target	Same as runlevel 3, but with a 
display manager.

6 (REBOOT)	reboot.target	Reboots the system.



Task:

Rescue Mode:
1. Interrupt the automatic selection of the default boot options in the GRUB menu.
2. Edit the kernel arguments to make the system boot into rescue or single user mode. 
3. Continue booting the system with your custom parameters. 
4. Reboot the system

How to do:
1. During system boot at the GRUB prompt press E
2. Find the kernel arguments line and append systemd.unit=rescue.target
3. Press Ctrl X to boot with custom parameters
4. The boot process completes.

Service Management:
1. List the running services on your system.
2. Check to see if the ssh service (daemon) is running on your system. 
3. Start, stop, and restart the ssh service.
4. Configure the ssh service to start automatically at boot time.
5. Shutdown the system.

How to do:
1. List the running services on your system.```bash
```bash
systemctl 
systemctl | grep running
```

2. Check to see if the ssh service (daemon) is running on your system. 
```bash
systemctl | grep ssh
```

3. Start, stop, and restart the ssh service.
```bash
systemctl start sshd
systemctl stop sshd
systemctl restart sshd
```
4. Configure the ssh service to start automatically at boot time.
```bash
systemctl enable sshd
systemctl is-enabled sshd
```
5. Shutdown the system.
```bash
systemctl poweroff
```

### Manage the Boot Process
Default boot loader for many modern Linux systems is a very powerful tool - GRUB (GRand Unified Bootloader). Briefly, a boot loader is the first software program that runs when a computer starts. It is responsible for loading and transferring control to the operating system kernel software (such as Linux kernel). The kernel, in turn, initializes the rest of the operating system. GRUB can load a variety of free operating systems, as well as proprietary operating systems with chain-loading (the mechanism for loading unsupported operating systems, such as DOS or Windows, by loading another boot loader). It is broken into at least two stages. The first stage is a small machine code binary on the MBR. Its sole job is to locate the second stage boot loader and load the first part of it into memory.  Once the second stage boot loader is in memory, it presents the user with a graphical screen showing the different operating systems or kernels it has been configured to boot. On this screen a user can use the arrow keys to choose which operating system or kernel they wish to boot and press Enter. If no key is pressed, the boot loader loads the default selection after a configurable period of time has passed. Once the second stage boot loader has determined which kernel to boot, it locates the corresponding kernel binary in the /boot/ directory. The kernel binary is named using the following format — /boot/vmlinuz-<kernel-version> file (where <kernel-version> corresponds to the kernel version specified in the boot loader's settings).
Most modern Linux versions are distributed with the GNU GRand Unified Boot loader (GRUB) version 2 boot loader, which allows the user to select an operating system or kernel to be loaded at system boot time. GRUB 2 also allows the user to pass arguments to the kernel.
Currently GRUB 2 has replaced what was formerly known as GRUB (i.e. version 0.9x), which has, in turn, become GRUB Legacy.
GRUB 2 reads its configuration from the /boot/grub2/grub.cfg file on traditional BIOS-based machines and from the /boot/efi/EFI/redhat/grub.cfg file on UEFI machines. 

This file contains menu information. The GRUB 2 configuration file, grub.cfg, is generated during installation, or by invoking the /usr/sbin/grub2-mkconfig utility, and is automatically updated by special command line tool for configuring GRUB, called grubby, each time a new kernel is installed. 

When regenerated manually using **grub2-mkconfig**, the file is generated according to the template files located in /etc/grub.d/, and custom settings in the /etc/default/grub file. 

Edits of **grub.cfg** will be lost any time **grub2-mkconfig** is used to regenerate the file, so care must be taken to reflect any manual changes in /etc/default/grub as well.

The `/etc/default/grub` file is used by the grub2-mkconfig tool.  
Any manual changes to **/etc/default/grub** require rebuilding the grub.cfg file.
Menu Entries in **grub.cfg**
Among various code snippets and directives, the grub.cfg configuration file contains one or more menuentry blocks, each representing a single GRUB 2 boot menu entry. These blocks always start with the menuentry keyword followed by a title, list of options, and an opening curly bracket, and end with a closing curly bracket. Anything between the opening and closing bracket should be indented. 

Each menuentry block that represents an installed Linux kernel contains linux on 64-bit IBM POWER Series, linux16 on x86_64 BIOS-based systems, and linuxefi on UEFI-based systems. Then the initrd directives followed by the path to the kernel and the initramfs image respectively. If a separate /boot partition was created, the paths to the kernel and the initramfs image are relative to /boot. In the example above, the initrd /initramfs-3.8.0-0.40.el7.x86_64.imgline means that the initramfs image is actually located at /boot/initramfs-3.8.0-0.40.el7.x86_64.img when the root file system is mounted, and likewise for the kernel path.

The GRUB 2 package contain commands for installing a bootloader and for creating a bootloader configuration file. 
grub2-install will install the bootloader - usually in the MBR, in free unpartioned space, and as files in /boot. The bootloader is installed with something like: 
grub2-install /dev/sda
grub2-mkconfig will create a new configuration based on the currently running system, what is found in /boot, what is set in /etc/default/grub, and the customizable scripts in /etc/grub.d/ . A new configuration file is created with: 
grub2-mkconfig -o /boot/grub2/grub.cfg
The configuration format has evolved over time, and a new configuration file might be slightly incompatible with the old bootloader.  It is thus often/always a good idea to run grub2-install before grub2-mkconfig for some reason is run. The RedHat installer, anaconda, will run these grub2 commands and there is usually no reason to run them manually. 
It is generally safe to directly edit /boot/grub2/grub.cfg in RedHat. Other distributions, in particular Debian/Ubuntu provide an update-grub command for activating your changes. Some customizations can be placed in /etc/grub.d/40_custom. 

More info at: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-working_with_the_grub_2_boot_loader



### Recover a forgotten root password on CentOS/Redhat 7 Linux Selinux system

The way on how you can reset a forgotten root password on a Linux system have not changed for many years. Resetting a root password on RHEL7 Linux system have not change much except that now we deal with SElinux and the system is now using systemd instead of init. Nevertheless, those who have already did reset root password on the Linux system will be with the following steps familiar. Here is the procedure of what needs to be done in order to recover a forgotten root password on Redhat 7 Linux:

1.	We need to edit GRUB2 boot menu and enter user single mode
2.	Next, we need to remount / partition to allow read and write
3.	Reset the actual root password
4.	Set entire system for SElinux relabeling after first reboot
5.	Reboot the system from a single mode
Now that we understand the procedure we can proceed with Redhat 7 password recovery.
1. Edit GRUB2 boot menu
Start your system and once you see your GRUB2 boot menu use e key to edit your default boot item. Usually it is the first line

Once you hit e key you will see a screen similar to the one below:

Depending on you terminal screen size you may see more or less information. In case you have a small terminal screen size note the little down pointing arrow on the right edge of your screen. The arrow means that more text is available when scrolling down.
 Scroll down and locate a line with rhgb quiet keywords

Move your cursor ( HINT: move to end of the line with CTRL+E ) on rhgb quiet keywords and replace them with  init=/bin/bash as show below:

Once you edit the boot line as show above press CTRL + x to start booting your RHEL 7 system into a single mode. At the end of the system boot you will enter a single mode:

2. Read&Write root partition remount
Once you enter a single your root partition is mounted as Read Only ro. 
In order to mount our partition with Read/Write flag we use mount with a remount option as follows:
```bash
mount -o remount,rw /
```
3. Change root's password
Still in the single mode we can proceed with the actual root password recovery. To do this we use passwd command:
```bash
passwd
```
4. SELinux relabeling
The additional step which needs to be taken on SELinux enables Linux system is to relabel SELinux context. If this step is ommited you will not be able to login with your new root password. The following command will ensure that the SELinux context for entire system is relabeled after reboot:
```bash
touch /.autorelabel
```
5. Sync disk changes and Restart
The final step is to sync the changes and physically restart the server it can be done by:
```bash
umount /
```
Another ways of sync is
```bash
sync
```
After restart you will be able to use your new root password.


How to Password Protect GRUB2 Boot Loader

Use grub2-setpassword to set a password for the root user :
```bash
grub2-setpassword
```
This creates a file /boot/grub2/user.cfg if not already present, which contains the hashed GRUB bootloader password. This utility only supports configurations where there is a single root user.


