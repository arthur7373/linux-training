# Linux Administration and Networking Basics (Level 2) <br> Լինուքսի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Managing Boot Process (SystemD)<br> Լինուքսի միացման գործընթացի կառավարում (SystemD)


### Linux Boot Process

Linux Boot Process generally include following steps:

1. After "Power ON", the hardware runs the firmware - <br>`BIOS` (Basic Input/Output System) <br>or<br> `UEFI` (Unified Extensible Firmware Interface). 
<br> `BIOS`/`UEFI` does the following: 
   1. Performs hardware checks - **POST** (Power-On Self-Test)
   2. Loads bootloader - **GRUB** (GNU GRand Unified Bootloader)
      1. **BIOS** → **MBR**
      2. **UEFI** → **GPT** 
2. Bootloader `GRUB` does the following:
   1. Provides menu to select boot system
   2. Loads the system - Linux kernel (PID 0).
3. Linux kernel (PID 0) does the System Initialization, which includes:
   1. Hardware checks on kernel level
   2. Starts the main Initialization Process - `SystemD/INIT` (PID 1) 
4. The main Initialization Process - `SystemD/INIT` (PID 1) does the following:
   1. Bring system to `default.target` / `default runlevel` , which includes:
      1. Mounting the filesystems
      2. Start different services
      3. Initialize user sessions
      ...


Linux system initialization for a long time was handled by the _Unix-inspired SystemV_ **init** 
process, which ran scripts to start services in a defined and configurable order to reach a 
series of states, called **runlevels**. 

Current most popular initialization system for Linux is  **SystemD**. 
It is more flexible and modular. 

First initialization process (PID 1) (**INIT** / **SystemD**) manages: 
* startup process
* services running (enable/disable, start/stop)
* shutdown process

**SystemD** uses **targets** instead of **runlevels** to define the state of the system. 

Goal of **runlevels**/**targets** is to process system initialization 
and bring the Linux system to specific state.

By default, there are two main targets:

**multi-user.target** - analogous to **runlevel 3**

**graphical.target** - analogous to **runlevel 5**


You can check the default target, which determines what services are started during boot:
```bash
systemctl get-default
```

Let us change to text mode
```bash
systemctl isolate multi-user.target (same as `init 3`)
```

Check the default target:
```bash
systemctl get-default
```

Let us change to graphical mode (same as `init 5`)
```bash
systemctl isolate graphical.target
```

If we want to permanently set default to text mode (like runlevel 3):

```bash
systemctl set-default multi-user.target && reboot
```

> 
> Alternative to `reboot` still is `init 6`
> Alternative to `poweroff` still is `init 0`
 

> Bonus tip:
> `echo $XDG_SESSION_TYPE`
> can give idea about type of session
> if returned `tty` then it is text mode
> if returned `x11` or `wayland` or something else it is most probably the graphical mode.


Permanently set default to graphical mode (like runlevel 5):
```bash
systemctl set-default graphical.target
reboot
```


You can also list the dependencies of a target
(to see which units run and certain target):

```bash
systemctl list-dependencies multi-user.target
```
You may see different colors:
* **Green** - unit is active/running.
* **White**/**gray** - unit is inactive/not running.
* **Red** - unit failed to run.

`systemctl` is a powerful command-line tool for managing `systemd`-driven Linuxes. 
It's a **system** and **service** manager for Linux. More details are below.


Table below presents **SystemV runlevel** and **SystemD target** equivalents.

| SystemV Runlevel | SystemD equivalent | Description
| --- | --- | --- |
| 0 (HALT)|poweroff.target |Shuts down the system |
| 1 (SINGLE-USER MODE) | rescue.target | Mode for administrative and system rescue tasks. Only the root user can log in. |
| 2 (MULTI-USER MODE) | | All users can log in, but network interfaces aren’t configured and networks services are not exported. Display manager is not started. |
| 3 (MULTI-USER MODE WITH NETWORKING) | **multi-user.target** | Starts the system normally. Display manager is not started. |
| 5 (START THE SYSTEM NORMALLY WITH APPROPRIATE DISPLAY MANAGER (WITHGUI)) | **graphical.target** | Same as runlevel 3, but with a display manager.|
| 6 (REBOOT) | reboot.target | Reboots the system.|

Old way to understand where you are was:

```bash
runlevel
```

It shows current and previous runlevel.




**Note:** Previous Linux versions, which were distributed with **SystemV init** ,
used init scripts located in the `/etc/rc.d/init.d/` directory. 
These **init** scripts were typically written in Bash, and allowed the system 
administrator to control the state of services and daemons in their system.
**SystemD** still can also run old _SystemV_ **init** scripts.
But **SystemD** driven systems have init scripts replaced with **service units**. 
**Service units** end with the **.service** file extension and serve a 
similar purpose as init scripts.

To view, `start`, `stop`, `restart`, `enable`, or `disable` system services, use the `systemctl` command as described below. 

The `service` and `chkconfig` commands are still available in some versions 
and work as expected, but are only included for compatibility reasons 
and better to be avoided. 

**Note**
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

#### Service Management:

1. List all services on your system.

```bash
systemctl 
```

2. List the running services on your system.

```bash
systemctl | grep running
```

3. Check to see if the service (daemon) is running on your system. 

```bash
systemctl | grep cron
```

4. Restart, Stop, Start the service.

```bash
systemctl restart crond
```

```bash
systemctl stop crond
```

```bash
systemctl start crond
```

5. Disable/Enable the service start automatically at boot time.

```bash
systemctl is-enabled crond
```

```bash
systemctl disable crond
```

```bash
systemctl is-enabled crond
```

```bash
systemctl enable crond
```

```bash
systemctl is-enabled crond
```

6. Reboot & shutdown the system

You can reboot with you can either run

```bash
reboot
```

or

```bash
init 6
```
You can shutdown with either

```bash
poweroff
```
or
```bash
init 0
```


> INTERESTING NOTE: 
> 
> Look at output of `ls -l /usr/sbin/reboot`
> 
> Look at output of `ls -l /usr/sbin/poweroff`

### PRACTICE

Create you own startup test service

```bash
cat  > /etc/systemd/system/startuptest.service  << "LASTLINE"
[Unit]
Description=Startup Test Service

[Service]
ExecStart=/opt/startuptest.sh

[Install]
WantedBy=multi-user.target
LASTLINE

```

Check its contents (HINT! you can use `cat` keyword to find config of any service)

```bash
systemctl cat startuptest
```

Create script mentioned in the service.

```bash
cat  > /opt/startuptest.sh  << "FINISH"
#!/bin/bash
echo "$(date) - Linux server $(hostname) started !" >> /var/log/startuptest.log
FINISH

chmod +x /opt/startuptest.sh

```

Now enable it to run at startup:

```bash
systemctl enable startuptest
```

Check:

```bash
systemctl is-enabled startuptest
```

Test run without restart
```bash
systemctl start startuptest
```

Check log file
```bash
cat /var/log/startuptest.log
```

Check in the list
```bash
systemctl list-dependencies multi-user.target | grep startuptest
```

Reboot

```bash
systemctl reboot
```

Login and check log file again.
You should see **one more line** from last startup.

```bash
cat /var/log/startuptest.log
```


&nbsp;
&nbsp;

## Recovering root password

Below is important way to recover root password.
While booting the server you should interrupt normal boot and do the following steps.

1. Press `Esc` to prevent GRUB automatic system load. Ensure first line is selected
at the GRUB prompt and press `e`
2. Scroll down and locate kernel arguments line (starting with `linux` or similiar and
 having generally `rhgb quiet` keywords. Move your cursor ( HINT: move to end of the line with CTRL+E ) on `rhgb quiet` keywords and replace them with `init=/bin/bash`
3. Press `Ctrl X` to boot with custom parameters. Kernel will boot and run single **bash** process. 
To make changes you need to remount root partition, since it is now mounted
in _read-only_ mode. In order to mount root partition with read/write flag 
we need to remount it as follows: `mount -o remount,rw /`
4. Now **any** changes can be done, like root password change with `passwd`
5. **IMPORTANT !** This additional step needs to be taken on SELinux-enabled systems to relabel SELinux context. 
   * Check  `cat /etc/selinux/config  | grep ^SELINUX=`  (if you don't get any lines as result it means this option is commented).
      if this option is set to `permissive`or `disabled`, you can omit this step.
      But if it is set to `enforcing`, then this step is very important. If not done **you will not be able to login**. 
      What you need to do is run the command:

   ```bash
   touch /.autorelabel
   ```
    It will ensure that the SELinux context for entire system is relabeled after reboot. So you can boot normally.

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

To remove GRUB password-protect from boot menu, simply delete the file `/boot/grub2/user.cfg`

>
> More info at:
> https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/assembly_protecting-grub-with-a-password_managing-monitoring-and-updating-the-kernel
> 

#### Manage the Boot Process (GRUB2)
GRUB 2 reads its configuration from the /boot/grub2/grub.cfg file on traditional BIOS-based machines and from the /boot/efi/EFI/redhat/grub.cfg file on UEFI machines. 

This file contains menu information. The GRUB 2 configuration file, grub.cfg, is generated during installation, or by invoking the /usr/sbin/grub2-mkconfig utility, and is automatically updated by special command line tool for configuring GRUB, called grubby, each time a new kernel is installed. 

When regenerated manually using **grub2-mkconfig**, the file is generated according to the template files located in /etc/grub.d/, and custom settings in the /etc/default/grub file. 

Edits of **grub.cfg** will be lost any time **grub2-mkconfig** is used to regenerate the file, so care must be taken to reflect any manual changes in /etc/default/grub as well.

The `/etc/default/grub` file is used by the grub2-mkconfig tool.  
Any manual changes to **/etc/default/grub** require rebuilding the grub.cfg file.
Menu Entries in **grub.cfg**

Among various code snippets and directives, the `grub.cfg` configuration file contains one or more `menuentry` blocks,
each representing a single GRUB 2 boot menu entry. 
These blocks always start with the `menuentry` keyword followed by a title, 
list of options, and an opening curly bracket, and end with a closing curly bracket. 
Anything between the opening and closing bracket should be indented. 

Each `menuentry` block that represents an installed Linux kernel contains linux on 64-bit IBM POWER Series, linux16 on x86_64 BIOS-based systems, and linuxefi on UEFI-based systems. Then the initrd directives followed by the path to the kernel and the initramfs image respectively. If a separate /boot partition was created, the paths to the kernel and the initramfs image are relative to /boot. In the example above, the initrd /initramfs-3.8.0-0.40.el7.x86_64.imgline means that the initramfs image is actually located at /boot/initramfs-3.8.0-0.40.el7.x86_64.img when the root file system is mounted, and likewise for the kernel path.


The GRUB 2 package contain commands for installing a bootloader and for creating a bootloader configuration file. 

grub2-install will install the bootloader - usually in the **MBR**, in free **unpartioned** space, and as files in /boot. 
The bootloader is installed with something like: 
`grub2-install /dev/sda`

grub2-mkconfig will create a new configuration based on the currently running system, what is found in /boot, what is set in /etc/default/grub, and the customizable scripts in /etc/grub.d/ . A new configuration file is created with: 

`grub2-mkconfig -o /boot/grub2/grub.cfg`
The configuration format has evolved over time, and a new configuration file might be slightly incompatible with the old bootloader.  It is thus often/always a good idea to run grub2-install before grub2-mkconfig for some reason is run. The RedHat installer, anaconda, will run these grub2 commands and there is usually no reason to run them manually. 
It is generally safe to directly edit /boot/grub2/grub.cfg in RedHat. Other distributions, in particular Debian/Ubuntu provide an update-grub command for activating your changes. Some customizations can be placed in /etc/grub.d/40_custom. 

More info at: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-working_with_the_grub_2_boot_loader

