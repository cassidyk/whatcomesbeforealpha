# Images #
## Assignment 2 ##
### Using DD with partitions ###

#### Prep ####
| To Do | Configuration |
| -- | -- |
| Partition | `sgdisk /dev/sdb --largest-new=1` |
| Filesystem | `mkfs.ext4 /dev/sdb1` |
| Mount | `mount /dev/sdb1 /mnt` |

#### The image file####
| BASH Code |
| -- |
| # Variables |
| X='a' |
| device="/dev/sd$X" |
| file="/mnt/sd$X.img.gz" |
| # Functions |
| count="parted -ms $device print &#124; tail -n+3 &#124; grep . -c" |

```
#!/bin/bash

# pulling the image
dd if=$device bs=4096 conv=notrunc,noerror,sync | pv | gzip -c -9 > $file

# pushing the image
gunzip -c $file | pv | dd of=$device bs=4096 conv=noerror,sync


# correct duplicate GUIDs as reported by lsblk
count=`eval $count`
sgdisk $device --randomize-guids
for (( partition=1; partition<=$count; partition++ ))
do
    tune2fs $device$partition -U random
done
```

#### Notes ####
```
lsblk -f # List block devices

genfstab -U -p /mnt > /mnt/etc/fstab # Overwrite fstab with new GUID
```

### Exercises ###
* [ Install Arch to /dev/sda1 ](https://wiki.archlinux.org/index.php/Beginners%27_guide)
* Create an image file of /dev/sda1
* Restore the image file to another partition.
* Modify /etc/fstab only for devices where the GUID was changed
* Split a partition in half/join two partitions together
