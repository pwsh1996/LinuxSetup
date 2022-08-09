# Install Arch

## Prerequisites 

Boot into Arch using the ISO https://archlinux.org/download/ (I'm using version 2022.08.05) (MD5:0e9943a96f8298abb0db5e64f76ad0be)

To check if you are using UEFI run `efivar -l` if your output is ```efivar: error listing variables: Function not implemented``` then you are not in UEFI mode and are using Legacy BIOS.<sub><a href="https://wiki.archlinux.org/title/Installation_guide#Verify_the_boot_mode">Verify the boot mode | ArchWiki </a></sub>

Check your network connection 'ping 1.1.1.1'

Update the clock using `timedatectl set-ntp true`

## Format Drives 

List disks `lsblk`

![image](https://user-images.githubusercontent.com/51274282/160964586-e5f00729-f1e0-449a-b162-93c1817b7f86.png)

To get info on on the drives use `hdparm -i /dev/sdb` it should output something like this

![image](https://user-images.githubusercontent.com/51274282/160965895-089a2fcb-a791-494f-8195-dc0ac24f59ef.png)

> Note: This wasn't working in my Gen 1 Hyper-V VM, it's output was `HDIO_GET_IDENTITY failed: invalid argument`

Wipe the disk using *gdisk* if there are already partitions on it

![image](https://user-images.githubusercontent.com/51274282/160976564-0c919196-ad91-404d-b0e6-1ef6044c7a03.png)

Formating the disk use *cgdisk* <br>
### Boot
Select **New** <br>
For the sector hit enter <br>
For the size I'd use **300MiB** <sub><a href="https://www.gnu.org/software/grub/manual/grub/html_node/BIOS-installation.html#BIOS-installation"> BIOS Installation | GNU </a></sub> <br>  <!-- Minimum is 31KiB, the GNU page recommends 1MiB, the Arch Wiki goes from 2MiB-300MiB, SomeOrdinaryGamer did 1024MiB and MentalOutlaw did 128MiB, will update if 300MiB isn't good-->
For the code **EF02**
For the name **boot**

### Swap
Select **New** <br>
For the sector hit enter <br>
For the size *At least 512MiB* <br>
For the code **8200** <br>
For the name **swap**

### Root
Select **New** <br>
For the sector hit enter <br>
For the size *~22GiB* <br>
For the code **8300** <br>
For the name **root**

### Home
Select **New** <br>
For the sector hit enter <br>
For the size *The rest of the space* <br>
For the code **8300** <br>
For the name **home**

Go down to *Write* then type `yes`

![image](https://user-images.githubusercontent.com/51274282/160976901-9a86d578-d3a7-48e1-a637-4bdb59b67192.png)

Format the boot partition as fat32
`mkfs.fat -F 32 /dev/sda1`

Format the swap partition 
`mkswap /dev/sda2`

To enable it use `swapon /dev/sda2`

To see if it is enabled

![image](https://user-images.githubusercontent.com/51274282/161163770-5fd6921e-b1ad-4fff-82bf-0c1f93f6eeea.png)

Then format the others as EXT4
```
mkfs.ext4 /dev/sda3

mkfs.ext4 /dev/sda4
```

## Mounting the Partitions

Mount the root partition `mount /dev/sda3 /mnt` 

Then `mkdir /mnt/boot` and `mkdir /mnt/home`

Then `mount /dev/sda1 /mnt/boot`

## Checking your mirrors

Backup your pacman mirrors file `cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak`

`pacman -Sy` to update the database

`pacman -S pacman-contrib` for the rankmirrors tool

`rankmirrors -n 6 /etc/pacman.d/mirrorlist.bak > /etc/pacman.d/mirrorlist`

## Installing

`pacstrap -i /mnt base base-devel linux linux-firmware linux-headers` (possibly run it without linux-firmware)

`genfstab -U -p /mnt >> /mnt/etc/fstab`

`arch-chroot /mnt` to change root into the mount

Install some extra packages *nano* and *man-db*

## Setting a few more preferences

`nano /etc/locale.gen` and the uncomment the line *en_US.UTF-8 UTF-8*

Run `locale-gen`

![image](https://user-images.githubusercontent.com/51274282/161170372-6fef749e-d1a8-4082-bf99-175da7a431ae.png)

`echo LANG=en_US.UTF-8 > /etc/locale.conf`

create a symbolic link `ln -sf /usr/share/zoneinfo/America/Chicago > /etc/localtime`

`hwclock --systohc`

Set the hostname `echo computer > /etc/hostname` to name it computer

Edit the /etc/pacman.conf to allow 32 bit applications, uncomment the lines about multilib

![image](https://user-images.githubusercontent.com/51274282/161172537-69b101b0-3023-4808-8393-1b898f5fba2e.png)

If it is an SSD enable trim service `systemctl enable fstrim.timer`

## Users

`passwd` to create the root password

Create your user `useradd -m -g users -G wheel,storage,power -s /bin/bash jmin` then make them a password `passwd jmin`

Modify suders file by `EDITOR=nano visudo` to view the file /etc/sudoers

## Boot Loader and Junk

if running UEFI `mount -t eficarfs efivarfs /sys/firmware/efi/efivars/`

to install bootctl

`bootctl install` then `nano /boot/loader/entries/default.conf`
```
title Arch Linux
linux /vmlinuz-linux
initrd /intel-ucode.img
initrd /initramfs-linux.img
```
`pacman -S intel-ucode`

`echo "options root=PARUUID=$(blkid -s PARTUUID -o value /dev/sda3) rw" >> /boot/loader/entries/default.conf`

To install Grub

pacman -S grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg



install package dhcpcd



## Sources
<a href="https://www.youtube.com/watch?v=H1ieRvLRxP0"> Some Ordinary Gamers | YouTube </a>

<a href="https://www.youtube.com/watch?v=rUEnS1zj1DM"> Mental Outlaw | YouTube </a>

<a href="https://www.youtube.com/watch?v=nSHOb8YU9Gw&t=720s"> Luke Smith | YouTube </a>

