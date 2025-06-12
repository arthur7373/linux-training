# Linux Administration and Networking Basics (Level 2) <br> Լինուքսի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Managing Hard Drives and File Systems<br>Դիսկերի և ֆայլային համակարգերի կառավարում

### Linux Filesystem Hierarchy Standard (FHS)


<pre>
🌳 /
├── 📁 bin                 # Essential user command binaries
├── 📁 boot                # Boot loader files (kernel, initramfs)
├── 📁 dev                 # Device files (hardware interfaces)
├── 📁 etc                 # System-wide configuration
│   ├── 📄 passwd          # User accounts
│   ├── 📄 shadow          # Encrypted passwords
│   ├── 📄 group           # Group definitions
│   ├── 📄 fstab           # Filesystem mounts
│   ├── 📄 hosts           # Network hostnames
│   └── 📄 resolv.conf     # DNS configuration
├── 📁 home                # User personal directories
│   ├── 📁 student         # Student's files
│   └── 📁 student2        # Student2's files
├── 📁 lib                 # Essential shared libraries
├── 📁 lib64               # 64-bit essential shared libraries
├── 📁 media               # Removable media mount points
│   ├── 📁 usb             # USB drives
│   └── 📁 cdrom           # Optical drives
├── 📁 mnt                 # Temporary manual mounts
├── 📁 opt                 # Optional software packages
├── 📁 proc                # Virtual process filesystem
├── 📁 root                # Root user's home
├── 📁 run                 # Runtime variable data
├── 📁 sbin                # System administration binaries
├── 📁 srv                 # Service data
├── 📁 sys                 # Virtual kernel objects
├── 📁 tmp                 # Temporary files
├── 📁 usr                 # Secondary hierarchy
│   ├── 📁 bin             # Non-essential binaries
│   ├── 📁 sbin            # Non-essential admin tools
│   ├── 📁 lib             # Libraries
│   ├── 📁 lib64           # 64-bit libraries
│   ├── 📁 include         # Development header files
│   ├── 📁 share           # Architecture-independent data
│   └── 📁 local           # Locally installed software
│       ├── 📁 bin         # Local binaries
│       ├── 📁 sbin        # Local admin binaries
│       ├── 📁 lib         # Local libraries
│       ├── 📁 lib64       # Local 64-bit libraries
│       ├── 📁 include     # Local header files
│       ├── 📁 share       # Local shared files
│       └── 📁 etc         # Local configuration
├── 📁 var                 # Variable data
    ├── 📁 log             # System logs
    │   ├── 📄 messages    # General logs
    │   ├── 📄 secure      # Authentication logs
    │   ├── 📄 maillog     # Mail logs
    │   └── 📄 cron        # Cron logs
    ├── 📁 cache           # Application cache
    ├── 📁 lib             # Dynamic libraries
    ├── 📁 spool           # Queued files (print, mail)
    ├── 📁 mail            # User mailboxes
    └── 📁 tmp             # Temporary files preserved between reboots

</pre>

### Linux Partition Mounting (հատվածների կցում)

* Լինուքսում Partition հատվածները կցվում են իրար կազմելով մեկ միասնական "ծառ", որում կա 
  * գլխավոր հատված՝ **Root partition**
  * մյուս հատվածները կցվում են (**mount**) գլխավորի որևէ կետին - դիրեկտորիային
* Յուրաքանչյուր Partition հատվածում տվյալներ պահպանելու համար այն պետք է ունենա որոշակի ստանդարտի ֆայլային համակարգ (format-արած լինի այդ ստանդարտով):


Օրինակ.

<pre>

🖴 Linux Server Storage Layout
├── 💽 Physical Disk 1 [/dev/sda] (500GB) 
│   ├── Partition 1 [/dev/sda1] (100GB, ext4) → /boot      # Boot partition
│   │
│   ├── Partition 2 [/dev/sda2] (300GB, ext4) → / (Root Filesystem)
│   │   ├── /etc
│   │   ├── /bin, /sbin
│   │   ├── /lib, /lib64
│   │   └── /root
│   │
│   └── Partition 3 [/dev/sda3] (100GB, ext4) → /var
│       ├── /var/log
│       ├── /var/cache
│       └── /var/lib
│
├── 💽 Physical Disk 2 [/dev/sdb] (1TB, HDD)
│   ├── Partition 1 [/dev/sdb1] (500GB, xfs) → /usr
│   │   ├── /usr/bin, /usr/sbin
│   │   ├── /usr/lib, /usr/lib64
│   │   ├── /usr/local
│   │   └── /usr/share
│   │
│   ├── Partition 2 [/dev/sdb2] (400GB, ext4) → /home
│   │   ├── /home/student
│   │   └── /home/student2
│   └── Partition 3 [/dev/sdb3] (100GB, swap) → [SWAP] (Virtual memory)
│
└─ 🌀 Virtual Filesystems (RAM-based)
   ├── devtmpfs  → /dev            # Kernel-managed dynamic device management
   ├── tmpfs     → /tmp            # Temporary directory
   └── tmpfs     → /run            # Runtime data (formerly was in /var/run - now symlink)

📌 Special Mounts:
    ├── /media → Auto-mounted removable media
    └── /mnt → Temporary manual mounts
</pre>




### Modern Linux Device Naming

In modern Linux systems, storage devices follow a predictable naming scheme:

* `/dev/sdX` - Traditional naming for SATA/SCSI/SAS drives (e.g., `/dev/sda`, `/dev/sdb`)

* `/dev/vdX`- for VirtIO block device (typically used in virtualized environments, such as KVM/QEMU on Proxmox or cloud VMs like DigitalOcean, etc. (e.g. `/dev/vda` ).

* `/dev/nvmeXnY` - For NVMe SSDs (e.g., `/dev/nvme0n1` - first NVMe device, first namespace)

* `/dev/mmcblkX` - For SD cards/eMMC storage (e.g., `/dev/mmcblk0`)


### Tools to detect current Drives/Partitions:


```bash
lsblk
```

```bash
lsblk -f
```

```bash
lshw -class disk -short
```

```bash
fdisk -l
```

```bash
parted -l
```

```bash
cat /proc/partitions
```

```bash
dmesg | grep "\[sd"
```



### Partition Table MBR / GPT 

A partition table is a critical data structure on a storage device (HDD, SSD, USB, etc.) 
that defines how the disk is logically divided into multiple logical sections (**partitions**, also called **volumes**).

Without it, the operating system wouldn’t know where partitions start/end and how data is organized.


Example of MBR-based partition table.

You can have maximum 4 Primary Partitions in MBR
If you need more you should create "Extended" -> "Logical"

<pre>

🖴 Disk: /dev/sda (500GB, MBR)
├── /dev/sda1 (Primary) → 200GB → / (Root FS)
├── /dev/sda2 (Primary) → 100GB → /home
├── /dev/sda3 (Extended) → 150GB → [Container for logical partitions]
│   ├── /dev/sda5 (Logical) → 50GB → /var
│   └── /dev/sda6 (Logical) → 100GB → /tmp
└── /dev/sda4 (Primary) → 50GB → [Swap]

</pre>


GPT (GUID Partition Table), the modern alternative to MBR

Key Improvements in GPT over MBR:

* Larger Disk Support:
  * MBR: Max 2TB disks 
  * GPT: Up to 9.4 ZB (zettabytes)

* More Partitions:
  * MBR: 4 primary (or 3 primary + 1 extended)
  * GPT: 128 standard (but can be increased)

* Data Redundancy:
  * GPT maintains backup header and partition table at disk end

  
Check partition table type

```bash
fdisk -l /dev/sda | grep Disklabel
```

### Partitioning and formatting/creating filesystem

Partitioning is done with tools like `fdisk`, `cfdisk`, `parted`, `gdisk` 
(partitions get new device names with numbers at the end: `/dev/sdb1`, `/dev/sdb2`)

Turn off your VM, create new device in VirtualBox and then start VM again
You should see it via

```bash
lsblk
```

Now you can create a partition and then filesystem

```bash
fdisk /dev/sdb
```

* Create partitions with `n`
* Check with `p`
* Write changes with `w`
* Exit with `q`


Other way is
```bash
/sbin/cfdisk /dev/sdb
```


Filesystem creation (formatting) is done with `mkfs` 
(<Tab><Tab> after `mkfs` to see installed variants.


```bash
/sbin/mkfs.ext4 /dev/sdb1
```

```bash
mkdir /work
```

Manual mounting:

```bash
mount  /dev/sdb1   /work
```

Check

```bash
df -hT
```


In order to be mounted at boot time we need to add configuration to `/etc/fstab`

Edit the `/etc/fstab` file to include the new partition. 
The new line should look similar to the following: 

```bash
echo "/dev/sdb1       /work                 ext4    defaults,noexec     0 0" >> /etc/fstab
```

Check

```bash
cat /etc/fstab
```

Now mount it, like it will be mounted at boot time.

```bash
mount  -a
```

`-a` option mounts ALL from `/etc/fstab`

Check

```bash
df -hT
```



> IMPORTANT !
> Mounting to a folder with existing data will temporary make it "hidden"

Alternatively you can find out drive UUID (Universally Unique Identifier) by 

```bash
lsblk -f
```

and replace first item in `/etc/fstab` like: `UUID=182ecbac-dd36-4113-b019-58f181c755a5` 

The advantage of using the UUID is that it is independent from the actual device number the operating system gives your hard disk. 

### fsck - Finding and Repairing Filesystem Corruption.

`fsck` is normally run at system startup. So it gets run automatically if the system was shut down uncleanly

It can also be run manually:  
```bash
fsck -y /dev/sdb1
```

Use `-y` to automatically answer ‘yes’ to any question. Without that option fsck will interactively ask whether to fix each problem as they are found (very annoying).
Use `-f` to force checking the filesystem, even if fsck thinks it is clean.

**Never run `fsck` on a mounted filesystem (unmount first).**

#### Simulate Disk Corruption and Fix

Unmount & Corrupt Journal

Unmount the partition (if mounted)
```bash
umount /dev/sdb1
```

Simulate a crash by clearing the journal
```bash
tune2fs -O ^has_journal /dev/sdX1
```

This makes ext4 think the filesystem was uncleanly unmounted.

Now run `fsck` 

```bash
fsck -y /dev/sdb1
```












