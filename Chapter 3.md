# Images #
## ICA 3 ##
### File Syncing ###

#### Prep ####
| To Do | Configuration |
| -- | -- |
| Create a user | `useradd -m -g users -G wheel -s /bin/bash $user` # passwd $user |
| Packages to install | `pacman -S base-devel` |
| BTSync | Installed with yaourt in next section |

| Shell Variables |
| -- |
| name='arch' |
| storage='\/root\/.sync' |
```
echo SUDONOVERIF=1 >> /etc/yaourtrc
su user -c 'yaourt -Sy btsync --noconfirm'

mkdir ~/.config
mkdir ~/.config/btsync
mkdir ~/.sync
btsync --dump-sample-config >> ~/.config/btsync/btsync.conf

# modify btsync.conf
sed -i "2s/.*/  \"device_name\" : \"$name\",/" ~/.config/btsync/btsync.conf
sed -i "10s/.*/  \"storage_path\" : \"$storage\",/" ~/.config/btsync/btsync.conf
sed -i '16s/.*/  "check_for_updates" : false,/' ~/.config/btsync/btsync.conf

# remove credentials
sed -i '31s/.*/  "listen" : "0.0.0.0:8888"/' ~/.config/btsync/btsync.conf
sed -i '32s/.*//' ~/.config/btsync/btsync.conf
sed -i '33s/.*//' ~/.config/btsync/btsync.conf
```

### Exercises ###
* use BTSync to transfer an image between computers
* use rsync to transfer an image between computers
