# Images #
## ICA 1 ##
### Build your work environment ###

#### Archlinux ####
| To Do | Configuration |
| -- | -- |
| Build | [ a VM with two 128 GB hard drives ](http://mirror.its.dal.ca/archlinux/iso/2014.05.01/)  |
| Packages to install | [ yaourt ](https://www.digitalocean.com/community/articles/how-to-use-yaourt-to-easily-download-arch-linux-community-packages#Method1:InstallviaCustomRepository), [ ssh ](https://wiki.archlinux.org/index.php/Ssh#Installing_OpenSSH), [ pv, lsscsi, ... ](https://wiki.archlinux.org/index.php/Pacman#Installing_packages) |
| Set password | `passwd root` |

#### Creating your partitions ####
The following shell script will format /dev/sda so it contains 8 partitions of size 16GiB (gibibytes). In this example the last partition will be 2048 sectors short of 16GiB due to GPT placing the secondary bootloader in the first section of disk.

| Shell Variables |
| -- |
| X='a' |
| device="/dev/sd$X" |
| filesystem='ext4' |
| end='+16G' |
| typecode='8300' |
| partition=1 |
| boot_start="sgdisk $device --first-aligned-in-largest" |
| boot_end="sgdisk $device --end-of-largest" |
| bios_type='EF02' |
| name=$(echo $device &#124; cut -c 6-9) |
| space=$(lsblk &#124; grep "$name" &#124; head -n 1 &#124; tr -s ' ' &#124; cut -d ' ' -f4 &#124; head -c -2) |
| max=$(($space / 16)) # HD Size / Partition Size |
| mkfs="mkfs.$filesystem $device$partition" |


```
#!/bin/bash

# zero out the device for better compression of images
pv /dev/zero | of=$device bs=1024 conv=noerror,sync

# section the device into parititons
for (( ; partition<$max; partition++ ))
do
    sgdisk $device --new=$partition:`eval $start`:$end --typecode=partnum:$typecode
    eval $mkfs
done
sgdisk $device --largest-new=$partition
eval $mkfs
(( partition++ ))
sgdisk $device --new=$partition:`eval $boot_start`:`eval $boot_end` --typecode=partnum:$bios_type
```

#### Note ####
To clear the partition table
```
sgdisk $device --clear
```
