# The Partition Table
| BASH Code |
| -- |
| # Variables |
| X='a' |
| source="/dev/sd$X" |
| layout='/mnt/partition_layout.bak' |
| destination='/mnt/partition_table.img' |
| # Functions |
| count=$(parted -ms $source print &#124; tail -n+3 &#124; grep . -c) |
| name=$(echo $source &#124; cut -c 6-9) |
| space=$(lsblk &#124; grep "$name" &#124; head -n 1 &#124; tr -s ' ' &#124; cut -d ' ' -f4 &#124; head -c -2) |
| formula=$((1024 + ($space * $count))) |

```
#!/bin/bash

# copy the partition layout
parted -ms /dev/sda print > $layout

# pulling the partition table
pv $source | dd of=$destination bs=1 count=$formula

# a push would therefore be
pv $destination | dd of=$source bs=1 count=$formula
```
