# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Managing Boot Process (SystemD)

Booting a Linux operating system on some device involves a sequence of events to complete startup:
1. **BIOS / POST**

When the device is powered on instructions stored in its firmware (called BIOS - Basic Input/Output System)
are first processed. BIOS performs Power On Self Test (POST) to 
ensure **hardware functionality** (eg: RAM is available). After passing POST, 
the BIOS searches through its **disk boot order**, loads, and executes 
the small intermediate program called bootloader.

2. **Bootloader**

Bootloader role is to **load the operating system** (such as Linux kernel). 
Default bootloader for many modern Linux systems is a very powerful tool - 
 **GRUB** (GNU GRand Unified Bootloader). GRUB can load a variety of operating systems.

GRUB may display splash screen with menu to select Linux kernel to boot. 
The splash screen will wait a few seconds to select and option. 
At this time boot process can be interrupted to enter single user mode (also known as rescue mode), 
used for recovery tasks such as resetting passwords.
If no key is pressed, GRUB will load the default kernel into RAM.
Thus Linux kernel becomes first running proccess (with PID **0**).

Most modern Linux versions are distributed with the GNU GRand Unified Boot 
loader (GRUB) version 2 boot loader, 
which allows the user to select an operating system or kernel 
to be loaded at system boot time.
Currently **GRUB2** has replaced what was formerly known as GRUB (i.e. version 0.9x),
which is now known as **GRUB Legacy**.

3. **Kernel**

Once the Linux kernel starts, it checks hardware from operating system point of view (enable needed drivers, etc.) and
then starts first initialization process (with PID **1**) with the responsibility of 
doing the rest system initialization (starting services and processes).
This initialization process runs until the system is shut down.

4. **System Initialization** ( **INIT** / **SystemD** )

Linux system initialization for a long time was handled by the _Unix-inspired SystemV_ **init** 
process, which ran scripts to start services in a defined and configurable order to reach a 
series of states, called **runlevels**. 

The **Upstart** initialization system was developed as a replacement to _SystemV_ that would 
trigger actions based on events, rather than running scripts in a particular order. **Upstart** was 
backwards compatible with _SystemV_ runlevels, and ran _SystemV_ init scripts.
 
Current most popular initialization system for Linux is  **Systemd**. 
It is more flexible and modular. 

**Systemd** uses **targets** instead of **runlevels**. 

Goal of **runlevels**/**targets** is to process system initialization 
and bring the Linux system to specific state.

By default, there are two main targets:

**multi-user.target** - analogous to **runlevel 3**

**graphical.target** - analogous to **runlevel 5**



First initialization process (**init** / **Upstart** / **SystemD**): 
- manages the system startup process
- manages the services running (enable/disable, start/stop)
- shuts the system down


**Note:** Previous Linux versions, which were distributed with **SystemV init** or **Upstart**,
used init scripts located in the `/etc/init.d/` directory. 
These **init** scripts were typically written in Bash, and allowed the system 
administrator to control the state of services and daemons in their system.
**Systemd** still can also run old _SystemV_ **init** scripts.
But **Systemd** driven systems have init scripts replaced with **service units**. 
**Service units** end with the **.service** file extension and serve a 
similar purpose as init scripts.

To view, `start`, `stop`, `restart`, `enable`, or `disable` system services, use the `systemctl` command as described below. 

The `service` and `chkconfig` commands are still available in the system 
and work as expected, but are only included for compatibility reasons 
and better to be avoided. 

Note
When working with system services, it is possible to omit this file `.service` unit
extension to reduce typing. When the `systemctl` utility encounters a unit name without a file extension, 
it automatically assumes it is a `.service` unit. 

For example: 

`systemctl status rsyslog.service`

is same as:

`systemctl status rsyslog`


### PRACTICE

#### Find out which initialization process (with PID **1**) is running in your Linux.
_HINT:_ You need to run command that shows all processes in **tree-like** manner and see the name of top process.

&nbsp;
&nbsp;

Table below presents **SystemV runlevel** and **Systemd target** equivalents.

| SystemV Runlevel | Systemd equivalent | Description
| --- | --- | --- |
| 0 (HALT)|poweroff.target |Shuts down the system |
| 1 (SINGLE-USER MODE) | rescue.target | Mode for administrative and system rescue tasks. Only the root user can log in. |
| 2 (MULTI-USER MODE) | | All users can log in, but network interfaces aren’t configured and networks services are not exported. Display manager is not started. |
| 3 (MULTI-USER MODE WITH NETWORKING) | **multi-user.target** | Starts the system normally. Display manager is not started. |
| 5 (START THE SYSTEM NORMALLY WITH APPROPRIATE DISPLAY MANAGER (WITHGUI)) | **graphical.target** | Same as runlevel 3, but with a display manager.|
| 6 (REBOOT) | reboot.target | Reboots the system.|

### PRACTICE

#### Service Management:

1. List the running services on your system.
```bash
systemctl 
systemctl | grep running
```

2. Check to see if the ssh service (daemon) is running on your system. 
```bash
systemctl | grep ssh
```

3. Stop, start, and restart the ssh service.
```bash
systemctl stop sshd
systemctl start sshd
systemctl restart sshd
```
4. Disabling/Enabling ssh service start automatically at boot time.
```bash
systemctl is-enabled sshd
systemctl disable sshd
systemctl is-enabled sshd
systemctl enable sshd
systemctl is-enabled sshd
```

5. Reboot & shutdown the system
You can reboot with you can either run
```bash
systemctl reboot
```
or
```bash
init 6
```
You can shutdown with either
```bash
systemctl poweroff
```
or
```bash
init 0
```
&nbsp;
&nbsp;
#### Recover root password:
1. Press `Esc` to prevent GRUB automatic system load. Ensure first line is selected
at the GRUB prompt and press `e`
2. Scroll down and locate kernel arguments line (starting with `linux` or similiar and
 having generally `rhgb quiet` keywords. Move your cursor ( HINT: move to end of the line with CTRL+E ) on `rhgb quiet` keywords and replace them with `init=/bin/bash`
3. Press `Ctrl X` to boot with custom parameters. Kernel will boot and run single **bash** process. 
To make changes you need to remount root partition, since it is now mounted
in _read-only_ mode. In order to mount root partition with read/write flag 
we need to remount it as follows: `mount -o remount,rw /`
4. Now **any** changes can be done, like root password change with `passwd`
5. **IMPORTANT !** This additional step needs to be taken on SELinux-enabled 
systems to relabel SELinux context. If not done **you will not be able to login** 
The following command will ensure that the SELinux context for entire system is relabeled after reboot:
`touch /.autorelabel`
6. Final important step is to `sync` the changes on disk. After that we can simply
power off the system (_no need to reboot, since there is no process which can do rebooting_)
  
_Same way can be used to do needed maintenance (eg. `fsck /dev/sda1` )_

#### How to Password Protect GRUB2 Boot Loader

Use `grub2-setpassword` to set a password for the `root` user (_it's not Linux `root`_)
```bash
grub2-setpassword
```
This creates a file `/boot/grub2/user.cfg` if not already present, 
which contains the hashed GRUB bootloader password. 
This utility only supports configurations where there is a single root user.

#### Manage the Boot Process (GRUB2)
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

