# Images #
## Assignment 1 ##
### Build your work environment ###

#### Archlinux ####
| To Do | Configuration |
| -- | -- |
| Build a VM | [ With two 128 GB and one 256 GB hard drives ](http://mirror.its.dal.ca/archlinux/iso/2014.05.01/)  |
| Packages to install | [ yaourt, ](https://www.digitalocean.com/community/articles/how-to-use-yaourt-to-easily-download-arch-linux-community-packages#Method1:InstallviaCustomRepository) [ ssh, ](https://wiki.archlinux.org/index.php/Ssh#Installing_OpenSSH) [ sshfs, ](https://wiki.archlinux.org/index.php/Sshfs) [ pv, lsscsi, ... ](https://wiki.archlinux.org/index.php/Pacman#Installing_packages) |
| Set password | `passwd root` |

#### Creating your partitions ####
This will format `$device` so it contains `$number` partitions of `$size`. The last partition will be smaller then the others unless the space for the partition table is accounted for.

| BASH Code |
| -- |
| # Variables |
| X='a' |
| device="/dev/sd$X" |
| filesystem='ext4' |
| size='16' |
| typecode='8300' |
| bios_typecode='EF02' |
| partition_count=1 |
| boot_start="sgdisk $device --first-aligned-in-largest" |
| boot_end="sgdisk $device --end-of-largest" |
| end=+"$size"G # in gibibyte |
| # Functions |
| name=$(echo $device &#124; cut -c 6-9) |
| space=$(lsblk &#124; grep "$name" &#124; head -n 1 &#124; tr -s ' ' &#124; cut -d ' ' -f4 &#124; head -c -2) |
| number=$(($space / $size)) # HD Size / Partition Size |
| # Eval |
| mkfs="mkfs.$filesystem $device$partition_count" |


```
#!/bin/bash

# zero out the device for better compression of images
pv /dev/zero | of=$device bs=4096 conv=noerror,sync

# section the device into parititons
for (( ; partition_count<$number; partition_count++ ))
do
    sgdisk $device --new=$partition_count:`eval $boot_start`:$end --typecode=partnum:$typecode
    eval $mkfs
done
sgdisk $device --largest-new=$partition_count
eval $mkfs
(( partition_count++ ))
sgdisk $device --new=$partition_count:`eval $boot_start`:`eval $boot_end` --typecode=partnum:$bios_typecode
```

#### Notes ####
```
sgdisk $device --clear # Clears the partition table

# pv defaults to using flags '-tpreb'
```

