# Install Arch

## Prerequisites 

Boot into Arch using the ISO https://archlinux.org/download/ (I'm using version 2022.03.01)

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

## Sources
<a href="https://www.youtube.com/watch?v=H1ieRvLRxP0"> Some Ordinary Gamers | YouTube </a>

<a href="https://www.youtube.com/watch?v=rUEnS1zj1DM"> Mental Outlaw | Youtube </a>

