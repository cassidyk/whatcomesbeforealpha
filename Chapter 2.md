# Images #
## ICA 2 ##
### Using DD with partitions ###

#### Prep ####
| To Do | Configuration |
| -- | -- |
| Mount | `mount /dev/sda8 /mnt` |
| List filesystem | `lsblk -f` |

#### The partition table ####
| Shell Variables |
| -- |
| X='a' |
| source="/dev/sd$X" |
| destination='/mnt/partition_table.img' |
| count=\`parted -ms /dev/sda print &#124; tail -n 1 &#124; cut -d':' -f1\` |
| formula=$((1024 + (128 * $count))) |

```
#!/bin/bash

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
| destination='/mnt/file.img' |

```
#!/bin/bash

pv -tpreb $source | dd of=$destination
```

#### Pushing an image ####

| Shell Variables |
| -- |
| X='a' |
| source='/mnt/file.img' |
| destination="/dev/sd$X" |
| count=\`parted -ms /dev/sda print &#124; tail -n 1 &#124; cut -d':' -f1\` |

```
#!/bin/bash

pv -tpreb $source | dd of=$destination bs=4096 conv=notrunc,noerror,sync

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
