
## Post-Gnome

### Networking
Should just work via gnome settings once NetworkManager is enabled. `/etc/NetworkManager/conf.d/20-clone.conf` will enable stable mac address cloning (one different address for each network), and keep the vendor ID portion of the mac. NetworkManager has some great features.

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

