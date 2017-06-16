
## Configuration
### Files
This repository contains configuration files needed for installation in `files/install-phase/`. You can use them as examples, copy them to `/etc`, or ignore them entirely. To copy them all:

    cp -r files/install-phase/etc/* /etc

If you are going to copy them all, a good time to do it is right after [unpacking the stage tarball](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage#Unpacking_the_stage_tarball).

### Profile
We use Gnome for a desktop environment, which requires using the systemd init system. If you don't mind a bit of extra work, you can use something else. That is one of the reasons we like Gentoo.

If you plan on using Gnome and systemd, make sure to select the right profile during the [Configuring Portage section](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Base#Choosing_the_right_profile) of installation. For example:

    eselect profile set default/linux/amd64/13.0/desktop/gnome/systemd

### Kernel
If you are using `LVM` and `LuKS`, you will need to use `genkernel-next` instead of `genkernel` to compile a kernel and/or initramfs. To compile a kernel, modules, and an initramfs:

    genkernel --install all

If you want to be able to edit the configuration, use menuconfig:

    genkernel --menuconfig --install all

### Logging/Cron
We use `syslog-ng` for logging, and `cronie` for cron. If you use something else, and you plan on using our `world` file, make a note that you will need to change it.

