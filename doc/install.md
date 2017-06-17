
## Installing Gentoo

### LUKS/LVM
This section can be used to replace a lot of the instructions in the [Preparing the disks section](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Disks) of the gentoo install documentation.  Create a grub

#### Important Note
The `/usr` directory can get quite large when compiling a kernel using the included configuration. 20G will not be enough. The filesystem size recommendations that follow were arrived at through extensive testing.

#### Arch Linux Wiki
Large parts of our dmcrypt and lvm setup were inspired by an [Arch Linux Wiki article](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system). The Arch Linux documentation is an invaluable resource to all Linux users, especially gentoo users.

#### Partitioning
Create a grub partition and boot partition as normal, and create a third partition that takes up the remainder of the disk. We will encrypt this, and create logical volumes on top of it.

#### LUKS/LVM setup
1. Format the partition with LUKS

```
cryptsetup luksFormat /dev/sda3
cryptsetup open /dev/sda3 cryptolvm
```

2. Then create a physical volume and a logical volume group:
```
pvcreate /dev/mapper/cryptolvm
vgcreate MyVG /dev/mapper/cryptolvm
```
3. Then create some LVM partitions:
##### For 250G+ drives:
```
lvcreate -L 8G MyVG -n swap
lvcreate -L 20G MyVG -n root
lvcreate -L 60G MyVG -n usr
lvcreate -L 40G MyVG -n var
lvcreate -l 100%FREE MyVG -n home
```

##### For smaller drives:
```
lvcreate -L 8G MyVG -n swap
lvcreate -l 100%FREE MyVG -n root
```

#### Create/Mount Filesystems
1. Start with swap:
```
mkswap /dev/mapper/MyVG-swap
swapon /dev/mapper/MyVG-swap
```

2. Then create the filesystems:
##### For 250G+ drives:
```
mkfs.ext4 /dev/mapper/MyVG-root
mkfs.ext4 /dev/mapper/MyVG-usr
mkfs.ext4 /dev/mapper/MyVG-var
mkfs.ext4 /dev/mapper/MyVG-home
```

##### For smaller drives:
```
mkfs.ext4 /dev/mapper/MyVG-root
```

3. Then mount everything:
##### For 250G+ drives:
```
mount /dev/mapper/MyVG-root /mnt/gentoo
mkdir /mnt/gentoo/usr
mount /dev/mapper/MyVG-usr /mnt/gentoo/usr
mkdir /mnt/gentoo/var
mount /dev/mapper/MyVG-var /mnt/gentoo/var
mkdir /mnt/gentoo/home
mount /dev/mapper/MyVG-home /mnt/gentoo/home
```

##### For smaller drives:
```
mount /dev/mapper/MyVG-root /mnt/gentoo
```

### Configuration

#### Files
This repository contains configuration files needed for installation in `gentoo-laptop/files/install-phase/`. You can use them as examples, copy them to `/etc`, or ignore them entirely. If you are going to copy them all, a good time to do it is right after [unpacking the stage tarball](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage#Unpacking_the_stage_tarball).

To copy them all:

    cp -ir gentoo-laptop/files/install-phase/etc/* /etc

#### Profile
We use Gnome for a desktop environment, which requires using the systemd init system. If you don't mind a bit of extra work, you can use something else. That is one of the reasons we like Gentoo.

If you plan on using Gnome and systemd, make sure to select the right profile during the [Configuring Portage section](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Base#Choosing_the_right_profile) of installation. For example:

    eselect profile set default/linux/amd64/13.0/desktop/gnome/systemd

#### Kernel
If you are using `LVM` and `LuKS`, you will need to use `genkernel-next` instead of `genkernel` to compile a kernel and/or initramfs. To compile a kernel, modules, and an initramfs:

    genkernel --install all

If you want to be able to edit the configuration, use menuconfig:

    genkernel --menuconfig --install all

`genkernel` will use the kernel configuration and `genkernel.conf` in `/etc`.

#### Logging/Cron
We use `syslog-ng` for logging, and `cronie` for cron. If you use something else, and you plan on using our `world` file, make a note that you will need to change it.

