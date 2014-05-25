# Images #
## ICA 2 ##
### Using DD with partitions ###

#### Prep ####
| To Do | Configuration |
| -- | -- |
| Mount | `mount /dev/sdb1 /mnt` # partition & mkfs.ext4 /dev/sdb1 if needed |
| List filesystem | `lsblk -f` |

#### The partition table ####
| Shell Variables |
| -- |
| X='a' |
| source="/dev/sd$X" |
| layout='/mnt/partition_layout.bak' |
| destination='/mnt/partition_table.img' |
| count=$(parted -ms $source print &#124; tail -n+3 &#124; grep . -c) |
| name=$(echo $source &#124; cut -c 6-9) |
| space=$(lsblk &#124; grep "$name" &#124; head -n 1 &#124; tr -s ' ' &#124; cut -d ' ' -f4 &#124; head -c -2) |
| formula=$((1024 + ($space * $count))) |

```
#!/bin/bash

# copy the partition layout
parted -ms /dev/sda print > $layout

# pulling the partition table
pv -tpreb $source | dd of=$destination bs=1 count=$formula

# a push would therefore be
pv -tpreb $destination | dd of=$source bs=1 count=$formula
```

#### Pulling an image ####
| Shell Variables |
| -- |
| X='a' |
| source="/dev/sd$X" |
| destination='/mnt/file.img.gz' |

```
#!/bin/bash

dd if=$source bs=1024 conv=notrunc,noerror,sync | pv | gzip -c -9 > $destination
```

#### Pushing an image ####

| Shell Variables |
| -- |
| X='a' |
| source='/mnt/file.img.gz' |
| destination="/dev/sd$X" |
| count="parted -ms $destination print &#124; tail -n+3 &#124; grep . -c" |

```
#!/bin/bash

gunzip -c $source | pv | dd of=$destination bs=1024 conv=noerror,sync

count=`eval $count`
# randomize guids for each partition
sgdisk $destination --randomize-guids
for (( partition=1; partition<=$count; partition++ ))
do
    tune2fs $destination$partition -U random
done
```

### Challanges ###
* [ Install Arch to /dev/sda1 ](https://wiki.archlinux.org/index.php/Beginners%27_guide)
* Create an image file of /dev/sda1
* Restore the image file to another partition.
* Write a script to update your bootloader to the new partition. What did you need to do to confirm the correct partition was booted?
* Complete the above for Windows 7
* Create a script that splits/joins adjacent partitions 
