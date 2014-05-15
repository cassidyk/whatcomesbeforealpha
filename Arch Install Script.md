# Script: Install Arch #
## Success Not Guaranteed ##
Before completing please read up on some trouble areas
* [ Choosing a partition type ](https://wiki.archlinux.org/index.php/Beginners%27_guide#Choose_a_partition_table_type)
* [ GPT versus MBR ](https://wiki.archlinux.org/index.php/Partitioning#Choosing_between_GPT_and_MBR)
* [ BIOS with GPT ](https://wiki.archlinux.org/index.php/GUID_Partition_Table#BIOS_systems)
* [ EFI with GPT ](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#EFI_System_Partition)

## Single partition setup ##
```
setfont Lat2-Terminus16
sed -i 's/#en_CA.UTF-8 UTF-8/en_CA.UTF-8 UTF-8/' /etc/locale.gen
sed -i 's/en_US.UTF-8 UTF-8/#en_US.UTF-8 UTF-8/' /etc/locale.gen
locale-gen
export LANG=en_CA.UTF-8

mount /dev/sda1 /mnt

pacstrap -i /mnt base

genfstab -U -p /mnt >> /mnt/etc/fstab
nano /mnt/etc/fstab # confirm it is correct

arch-chroot /mnt /bin/bash
```

## Multi partition setup ##

```
setfont Lat2-Terminus16
sed -i 's/#en_CA.UTF-8 UTF-8/en_CA.UTF-8 UTF-8/' /etc/locale.gen
sed -i 's/en_US.UTF-8 UTF-8/#en_US.UTF-8 UTF-8/' /etc/locale.gen
locale-gen
export LANG=en_CA.UTF-8

cgdisk /dev/sda # if partitions not created

mkswap /dev/sda1
swapon /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mount /dev/sda2 /mnt
mkdir /mnt/home
mount /dev/sda3 /mnt/home

pacstrap -i /mnt base

genfstab -U -p /mnt >> /mnt/etc/fstab
nano /mnt/etc/fstab # confirm it is correct

arch-chroot /mnt /bin/bash
```

## Continue from arch-chroot ##
```
sed -i 's/#en_CA.UTF-8 UTF-8/en_CA.UTF-8 UTF-8/' /etc/locale.gen
locale-gen
echo LANG=en_CA.UTF-8 > /etc/locale.conf
export LANG=en_CA.UTF-8

setfont Lat2-Terminus16
echo FONT=Lat2-Terminus16 > /etc/vconsole.conf

ln -s /usr/share/zoneinfo/Canada/Mountain /etc/localtime
hwclock --systohc --utc

echo Arch > /etc/hostname   # set hostname
systemctl enable dhcpcd.service
passwd root # set root login

pacman -S grub

grub-install --target=i386-pc --recheck --force /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
exit
```
