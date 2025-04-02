# Creating a new disk partition using the Linux CLI. 

So you just installed Linux and you've got a shiny new drive with unallocated space you'd like to utilize. 

### Find the information for the Disk

You can use the built in GUI utilities like `Disks` in Ubuntu but I find the easiest way to see everything is from the command line, plus it's good practice since you won't always have a lovely GNOME environment to hold your hand. 

Let's start by getting the list of block devices from the filesystem. You should see something like this, the name of my free disk is what we're looking for: **sdb**.
```bash
$ lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
sda 8:0 0 465.8G 0 disk 
├─sda1 8:1 0 464.7G 0 part / 
└─sda2 8:2 0 1G 0 part /boot/efi 
sdb 8:16 0 931.5G 0 disk 
nvme0n1 259:0 0 238.5G 0 disk 
└─nvme0n1p1 259:1 0 238.5G 0 part /home
```

### Edit the partition tables
In order to create a partition on the disk we will use `fdisk` to edit the partition table. This is an interactive tool, the steps below will create a single primary partition using the full disk, but you can adjust them based on your own needs.

```bash
sudo fdisk /dev/sdb
```

Inside `fdisk`, do the following:

- Press **n** (new partition).
    
- Choose **p** (primary partition).
    
- Press **Enter** to accept the default partition number.
    
- Press **Enter** twice to use the full disk space.
    
- Press **w** to write the changes and exit.

With the partition tables updated with our new partition we need to format it. 

### Format and Mount partition

We will use `mkfs` to format the partition in `ext4`, you can research other formats but this one is pretty standard. 

```bash
sudo mkfs.ext4 /dev/sdb1
```

From there we need a mount point. I am using this drive to just store data for various applications since it's a slower HDD with a larger capacity. This is just a folder created with `mkdir`, the `-p` flag creates any parent directories if they do not exist in your provided path.

```bash
sudo mkdir -p /data
```

From there we simply mount the partition and it's almost ready to go.

```bash
sudo mount /dev/sdb1 /data
```

### Make the route permanent with fstab

We aren't done yet though, to make our mount permanent we'll need to add it to the `fstab` so it persists on reboot. **Be very careful with what you do here.** Double check the changes you make as a mistake could be very costly. 

To do this we will need to find the `UUID` unique to the device, since in theory the `/dev/sdb1` name could change.

```bash
$ blkid /dev/sdb1
/dev/sdb1: LABEL="Data" UUID="XXXX-XXXX-XXXX-XXXX" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="YYYYYYYY-YY"
```

I've censored my output, yours will look different. What you want is the `UUID`. 

```bash
# Open the file for editing
sudo nano /etc/fstab

# Add the following line:
UUID=<your-uuid> /data ext4 defaults 0 2
```

Test your mount for errors before rebooting. 
```bash
sudo mount -a
```
### Conclusion

Congratulations! You should hopefully now have a brand new filesystem partition all configured and mounted to wherever you chose. In order to check on your new partition, run another `lsblk` to see the mount point on the device and other info.

```bash
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
...
sdb           8:16   0 931.5G  0 disk 
└─sdb1        8:17   0 931.5G  0 part /data
...
```

We can also check the amount of free space on the mount location using `df`, the `-h` flag for a human readable format is nice here. 

```bash
$ df -h | grep /data
/dev/sdb1       916G   36K  870G   1% /data
```

### A note on permissions

It's very likely that the permissions for `/data` that we've just created will only be accessible by `root`. If you want to change this I would first check to see what the permissions are with something like `ll /data`. 

#### To set the ownership to your own user account:

```bash
sudo chown -R $USER:$USER /data
```

#### To set full write access (insecure option):
```bash
sudo chmod -R 777 /data
```

For further refinement look into `chmod`, `chown`, `usermod`, and `groupadd` commands to manipulate permissions for directories and implement user groups for granting permissions. 

Thanks for reading!