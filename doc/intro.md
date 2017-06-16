
## Introduction
This is a repository of configuration files and resources for installing Gentoo Linux on laptops.

### The State of Things
This is a work in progress. There are still some gaps in between sections. This is meant to be a companion to the [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page).

### The Kernel
The kernel and initramfs are compiled using genkernel-next. Kernel configuration is largely based on the [Kali Linux](https://www.kali.org/) configuration, which has excellent laptop hardware support.

The kernel is set up for intel core processors; if installing on another processor type, change the processor type in menuconfig when compiling a kernel.

### 2 in 1s
This has been tested on at least one 2in1 laptop, and everything works. Laptop hardware support has come a long way since the 1990s, in everything from the kernel to desktop environments.

### Disk Encryption
Full disk and swap encryption are a necessity in a lot of industries. This guide uses LUKS on LVM to encrypt everything except the boot partition.

