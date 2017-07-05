# Trace Linux Tests

## VM install #1
### Goals
- Test config file sanity
- Test install time
- Work out choices/options
    - Build test install script

### Versions
- Test date: 2017-07-04
- Trace Linux: 0.4-beta pre-release
- Gentoo install media: install-amd64-minimal-20170629.iso
- Gentoo stage tarball: stage3-amd64-minimal-20170629.tar.bz2

### Host Specs
- Processor: Intel core i5
- RAM: 16G
- Storage: SSD
- Network: Wifi
- OS: Ubuntu 16
- Virtualization: VirtualfBox 5

### VM Specs
- Processors: 2
- Execution cap: 100%
- Ram: 8G
- Storage: .vmdk on IDE controller
- Network: redundant (NAT/bridged adapter)

### Times
- Start time: 2017-07-04 16:00
- First `@world` update: 3 hours
- Second `@world` update: 30 minutes
- `@trace-linux-kernel` emerge: 10 minutes
- `genkernel --install all`: < 3 hours
- Gnome: 6.5 hours
- Total (up to Gnome): ~12.5 hours
- `@trace-linux-core`: 25 minutes
- `@trace-linux-net`: 5 minutes
- `@trace-linux-disk`: 5 minutes
- `@trace-linux-gui`: 5h 20m

### Choices:
- Drive
- Drive size: use blockdev --getsize64 /dev/DRIVE
    - Alternate `fstab` for <240G drives
- Time zone (Installing the Gentoo base system)
    - `/etc/timezone`
    - Try not setting - doesn't transfer to systemd
- Locales
    - `/etc/locale.gen`
    - Default to US ISO and UTF
    - Include config file in files
- Hostname (Configuring the system)
    - /etc/conf.d/hostname
    - Don't bother during scripted install, will be forgotten
- Root password
    - Set just before reboot in script (interactive, do not save)
- Cron/syslog (Installing system tools)
    - Default to cronie, syslog-ng

### Notes
- `/etc/defaults/grub` was overwritten during grub install
    - `grub-install` and `grub-mkconfig` had to be re-run
    - Wrong `/usr` in `fstab` might have contributed
- `timedatectl set-time "YYYY-mm-dd HH:MM:SS"` needed after reboot
    - Was way off: 8 hours 30 minutes fast
- Hostname needs to be reset after first reboot into systemd
- Use seperate drives for tmp stuff next time
    - `/usr/src`
    - `/var/tmp`
    - `/usr/portage/distfiles`
- Split up `@trace-linux-gui` set
    - Reove blender, virtualbox, openoffice
        - Form other sets
    - Rename to `@trace-linux-netgui`
- Set default Gnome extensions with eselect during install

