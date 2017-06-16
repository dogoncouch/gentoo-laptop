
## LUKS/LVM
This section can be used to replace a lot of the instructions in the gentoo install documentation.

### Important Note
The `/usr` directory can get quite large when compiling a kernel using the included configuration. 20G will not be enough. The filesystem size recommendations that follow were arrived at through extensive testing.

### Arch Linux Wiki
Large parts of our dmcrypt and lvm setup were inspired by an [Arch Linux Wiki](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system) article. The Arch Linux documentation is an invaluable resource to all Linux users, especially gentoo users.

### LUKS/LVM setup
1. Format the partition with LUKS
```
cryptsetup luksFormat /dev/sdxX
cryptsetup open /dev/sdxX cryptolvm
```

2. Then create a physical volume and a logical volume group:
```
pvcreate /dev/mapper/cryptolvm
vgcreate MyVG /dev/mapper/cryptolvm
```
3. Then create some LVM partitions:
#### For 250G+ drives:
```
lvcreate -L 8G MyVG -n swap
lvcreate -L 20G MyVG -n root
lvcreate -L 60G MyVG -n usr
lvcreate -L 40G MyVG -n var
lvcreate -l 100%FREE MyVG -n home
```

#### For smaller drives:
```
lvcreate -L 8G MyVG -n swap
lvcreate -l 100%FREE MyVG -n root
```

### Create/Mount Filesystems
1. Start with swap:
```
mkswap /dev/mapper/MyVG-swap
swapon /dev/mapper/MyVG-swap
```

2. Then create the filesystems:
#### For 250G+ drives:
```
mkfs.ext4 /dev/mapper/MyVG-root
mkfs.ext4 /dev/mapper/MyVG-usr
mkfs.ext4 /dev/mapper/MyVG-var
mkfs.ext4 /dev/mapper/MyVG-home
```

#### For smaller drives:
```
mkfs.ext4 /dev/mapper/MyVG-root
```

3. Then mount everything:
#### For 250G+ drives:
```
mount /dev/mapper/MyVG-root /mnt/gentoo
mkdir /mnt/gentoo/usr
mount /dev/mapper/MyVG-usr /mnt/gentoo/usr
mkdir /mnt/gentoo/var
mount /dev/mapper/MyVG-var /mnt/gentoo/var
mkdir /mnt/gentoo/home
mount /dev/mapper/MyVG-home /mnt/gentoo/home
```

#### For smaller drives:
```
mount /dev/mapper/MyVG-root /mnt/gentoo
```

### Links
- [LuKS/LVM: Arch Linux Wiki](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system)
- [Dm-crypt: Gentoo Wiki](https://wiki.gentoo.org/wiki/Dm-crypt_full_disk_encryption)

