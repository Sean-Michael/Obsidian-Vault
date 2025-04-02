`lsblk`
`sudo fdisk /dev/sdb`
Inside `fdisk`, do the following:

- Press **n** (new partition).
    
- Choose **p** (primary partition).
    
- Press **Enter** to accept the default partition number.
    
- Press **Enter** twice to use the full disk space.
    
- Press **w** to write the changes and exit.
`sudo mkfs.ext4 /dev/sdb1`
`sudo mkdir -p /data`
`sudo mount /dev/sdb1 /data`
`sudo nano /etc/fstab`
`/dev/sdb1 /data ext4 defaults 0 2`
`df -h | grep /data`