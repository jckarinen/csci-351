# Homework 2 Notes

## Creating, Formatting, and Mounting Partitions in Unix/Linux

### Why partition?
Partitioning divides a storage device into separate, logical sections.\
Some reasons we might want to create partitions:
- To organize our system logically
- To use different filesystem types for different purposes
- To separate system data from user data, e.g. being able to reinstall our operating system while keeping documents and media
- To boot multiple operating systems, e.g. Windows and Linux on the same computer
- Security reasons
- Much more (probably)

### Steps to take before partitioning
Use the `fdisk` tool.

First, you can use `fdisk -l` to view information about your disks:

```
[root@kvm-karinen ~]# fdisk -l
Disk /dev/vda: 100 GiB, 107374182400 bytes, 209715200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x27bfec5e

Device     Boot     Start       End  Sectors  Size Id Type
/dev/vda1  *         2048   2099199  2097152    1G 83 Linux
/dev/vda2         2099200 100558847 98459648 46.9G 83 Linux
/dev/vda3       100558848 104851455  4292608    2G 82 Linux swap / Solaris


Disk /dev/zram0: 1.24 GiB, 1332740096 bytes, 325376 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

Ignore the RAM disk and look at the disk named `/dev/vda`. We can see that this disk has a total size of `100 GiB` and that it currently has three partitions: `vda1`, `vda2`, and `vda3`. If we add up the sizes of these three partitions, we can see that they take up a combined ~50 GiB. Thus, we have about 50 GiB of unused space on `vda`.

### Partitioning the disk using fdisk
Start `fdisk` with `fdisk <device_path>`. In this case, we will use `fdisk /dev/vda`.

The program is straightforward to use. Type `m` to see the menu of available commands. Create your partition with the desired size. Once you're done, make sure to enter `w` to write the changes to the disk. Afterward, you can use `fdisk -l /dev/vda` to verify that the new partition is correct.

### Formatting the partition with a filesystem
In order to use our new space, we need to structure it in a way that the operating system can understand. This is where **formatting** comes in. Formatting will create a filesystem structure on the partition so that we can read and write files. Linux supports many different filesystem types.

We'll format the partition with the ext4 filesystem. To do so, use the command `mkfs.ext4 <partition_path>`, in this case `mkfs.ext4 /dev/sda4`. **Make sure that the path has been entered correctly**, otherwise you may mistakenly wipe one of your existing filesystems.

### Mounting the partition
Mounting allows us to access the files and directories on a partition. In order to mount a partition, we need to tell the operating system:
1. What partition we're talking about 
2. What filesystem is on it (however, this is usually checked automatically)
3. Where we want to be able to access it

The command to mount a partition is `mount <partition_path> <access_path>`, where `access_path` is the directory we want to access the filesystem at (this could be anywhere on our system).
- Note that we do not need to specify the filesystem type, because it's done automatically. However, if you need to manually specify the type, you can use the `-t` flag to do so, e.g. `mount -t ext4 /dev/sda4 /backup/`. 

Now we can access the new filesystem in the `/backup/` directory.

### Show current mount points
We can use `df -h` to view information about disk space usage, including where each filesystem is mounted.

### Mount automatically
Currently, we have manually mounted the new partition. However, when we reboot the system, the partition will become unmounted again. We want to configure our system to automatically mount the partition on boot.

To achieve this, we need to edit the configuration file located at `/etc/fstab`. The "fstab" stands for "File System Table," and the system uses this file to figure out which filesystems to mount when it boots.

The file is formatted like this:

```
UUID=9c1c3bcd-d503-43a6-917d-5e3fb0ba1a92 /                       xfs     defaults        0 0
UUID=db57507d-8012-44fc-9e48-cd45d5f0533e /boot                   xfs     defaults        0 0
UUID=bad550fc-fb86-4567-bd43-2991589ff59d none                    swap    defaults        0 0
```

The first column holds the UUID or unique identifier for each partition. We could also use the filesystem path (e.g. `/dev/sda2`), but using the UUID ensures that if the name changes, the same *device* will still be mounted.

The second column (e.g. `/boot` in row 2) holds the **mount point**. This is the location where we mount the filesystem.

The third column tells us the filesystem type, e.g. `xfs`.

The next column contains mount options - just use `defaults` if you don't care. The second-to-last column specifies whether or not you want to back up the filesystem with the `dump` command. The last column determines what order the system should check our filesystems in when it boots.

In order to obtain the UUID of our new filesystem to add it to `fstab`, we can use the `blkid` command. Copy the UUID and create a new entry in `fstab`. Then you're done.

### Testing it out
First, unmount the filesystem with `umount /backup`. Then use `mount -a`. The `-a` flag instructs `mount` to mount everything in our `fstab`.

### Additional notes
In order to redirect the output of a command into vim, you can use something called "Process Substitution." (https://tldp.org/LDP/abs/html/process-sub.html). It looks like this:

```
vi <(ls -la)
```

The above example command lets us send the command-line output of `ls -la` into vim to do whatever we want with it.

Also, the command looks like a bird: `<(-)`












