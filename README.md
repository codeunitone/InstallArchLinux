# Install Arch Linux

## About




## Partition Layout and Filesystem

* use fdisk
* Create a GPT partion table
* create following partitios

### /dev/sda1

* __Size:__ 250 MB
* __Mountpoint:__ /boot/efi
* __Lable:__ EFI
* __Partition Type:__ EF00
* __Filesystem:__ FAT32

```
mkfs.vfat -F 32 -n EFI /dev/sda1
```

### /dev/sda2

* __Size:__ Disksize - swap - boot partition
* __Mountpoint:__ /
* __Lable:__ ROOT
* __Partition Type:__ 8300
* __Filesystem:__ BTRFS

```
mkfs.btrfs -f -L ROOT /dev/sda2
```

### /dev/sda3

* __Size:__ size of memory to enable hibernate mode
* __Mountpoint:__ -
* __Lable:__ SWAP
* __Partition Type:__ 8200
* __Filesystem:__ swap

```
mkswap -L SWAP /dev/sda3
swapon /dev/sda3
```


## Create BTRFS Subvolumes 

First mount the root partition under /mnt

```
mount /dev/sda2 /mnt
```

Create subvolumes
```
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@pkg
btrfs subvolume create /mnt/@snapshots

```

Create mount points for the subvolumes
```
cd ..
umount /mnt
mount -o noatime,compress=lzo,space_cache,ssd,subvol=@ /dev/sda2 /mnt

mkdir -p /mnt/{boot/efi,home,var/log,var/cache/pacman/pkg,btrfs}

mount /dev/sda1 /mnt/boot/efi
mount -o noatime,compress=lzo,space_cache,ssd,subvol=@home /dev/sda2 /mnt/home
mount -o noatime,compress=lzo,space_cache,ssd,subvol=@pkg /dev/sda2 /mnt/var/cache/pacman/pkg
mount -o noatime,compress=lzo,space_cache,ssd,subvol=@log /dev/sda2 /mnt/var/log
mount -o noatime,compress=lzo,space_cache,ssd,subvolid=5 /dev/sda2 /mnt/btrfs
``` 

## Install Base System

```
pacstrap /mnt base base-devel bash-completion btrfs-progs dosfstools grub efibootmgr 
```

## Generate fstab

generate fstab from mounted volumes
```
genfstab -Lp /mnt >> /mnt/etc/fstab
```

open /mnt/etc/fstab  


## Configure system

https://wiki.archlinux.org/index.php/Installation_guide#Configure_the_system
