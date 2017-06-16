# Gentoo For Laptops

- [Introduction](#introduction)
- [LUKS/LVM](#lukslvm)
- [Gnome](#gnome)
- [Post-Gnome](#post-gnome)
- [Resources/Thanks](#resourcesthanks)


## Introduction
This is a repository of configuration files and resources for installing gento Linux on laptops.

### The State of Things
This is a work in progress. There are currently a lot of gaps in between sections. This is meant to be a companion to the [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page).

### The Kernel
The kernel and initramfs are compiled using genkernel-next. Kernel configuration is largely based on the [Kali Linux](https://www.kali.org/) configuration, which has excellent laptop hardware support.

The kernel is set up for intel core processors; if installing on another processor type, change the processor type in menuconfig when compiling a kernel.

### 2 in 1s
This has been tested on at least one 2in1 laptop, and everything works. Laptop hardware support has come a long way singe the 1990s, in everything from the kernel to desktop environments.

### Disk Encryption
Full disk and swap encryption is a necessity in a lot of industries. This guide uses LUKS on LVM to encrypt everything except the boot partition.


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


## Gnome
For this phase, do whatever you did to get networking to work on the install medium.

1. Copy files into `/etc`:
```
cp -r files/desktop-install-phase/etc/* /etc
```

2. Update the `@world` set with new package.use settings:
```
emerge --ask --newuse --deep @world
```

3. Install gnome:
```
emerge --ask -v gnome
```

4. Enable/start GDM:
```
systemctl enable --now gdm
```


## Post-Gnome

### Networking
Should just work via gnome settings once NetworkManager is enabled. Our `/etc/NetworkManager/conf.d/20-clone.conf` will enable stable mac address cloning (one different address for each network), and keep the vendor ID portion of the mac. NetworkManager has some great features.

```
systemctl enable --now NetworkManager
```

### Bluetooth:
Use the [Gentoo Wiki article](https://wiki.gentoo.org/wiki/Bluetooth) to get bluetooth working. Our kernel contains a lot of bluetooth hardware modules, so the kernel configuration should be ok.

There is an issue with bluetooth and Gnome. Gnome seems to use rfkill to turn off the bluetooth interface, and then for some reason it disappears out of the menu and has to be unblocked manually, or through the settings GUI.

If bluetooth isn't working, make sure it is running/up/unblocked:

```
rfkill unblock bluetooth
hciconfig bluetooth up
systemctl start bluetooth
```

To Do: Full bluetooth control via gnome

### Screen rotation:
#### Install iio-sensor-proxy
`iio-sensor-proxy` handles accelerometers and light sensors, and is used for screen rotation and automatic brightness control, if the system has the necessary sensors. Our kernel includes a lot of modules for sensors.

First, download the [iio-sensor-proxy ebuild](https://bugs.gentoo.org/show_bug.cgi?id=565904). Then put it in the `/usr/portage` tree:

    mkdir /usr/portage/sys-apps/iio-sensor-proxy
    cp iio-sensor-proxy-2.0.ebuild /usr/portage/sys-apps/iio-sensor-proxy

Then emerge it:

    emerge sys-apps/iio-sensor-proxy

#### Using iio-sensor-proxy
The iio-sensor-proxy service has to be started at first; after a while it becomes static, and no longer needs to be started.

```
systemctl start iio-sensor-proxy
```

In the beginning, it may not work on startup until after a suspend/resume cycle. That issue seems to go away on its own after a few days/weeks.

##### Links
- [iio-sensor-proxy ebuild](https://bugs.gentoo.org/show_bug.cgi?id=565904)
- [iio-sensor-proxy GitHub](https://github.com/hadess/iio-sensor-proxy)


## Resources/Thanks

### General
- [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page)
- [Kali Linux](https://www.kali.org/)

### LuKS/LVM
- [LuKS/LVM: Arch Linux Wiki](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system)
- [Dm-crypt: Gentoo Wiki](https://wiki.gentoo.org/wiki/Dm-crypt_full_disk_encryption)

### iio-sensor-proxy
- [iio-sensor-proxy ebuild](https://bugs.gentoo.org/show_bug.cgi?id=565904)
- [iio-sensor-proxy GitHub](https://github.com/hadess/iio-sensor-proxy)

### Bluetooth
- [Bluetooth: Gentoo Wiki](https://wiki.gentoo.org/wiki/Bluetooth)

