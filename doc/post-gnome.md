
## Post-Gnome

### Config Files
If you want to use our setup, you can copy all of the files in `files/final-phase` to your filesystem. There are a few steps:

1. Create a backup of your `world` set:

    cp /var/lib/portage/world ~/world.bak

Do the same for your genkernel.conf, if you have made any changes to it. Look at all of the files in `files/final-phase/`, and back up any other files you have changed.

2. Copy everything:

    cp -ir files/final-phase/* /

3. Add anything from your old `world` set. Use `diff` to see the difference:

    diff ~/world.bak /var/lib/portage/world

The lines that start with a `<` were in your original world set, and need to be added (without the `<` or the space after it) to the new world set.

Merge in any other changes you backed up earlier.

### Networking
Networking should just work via gnome settings once NetworkManager is enabled (you might have to disable whatever you did to get it working for the Gnome install). Our `/etc/NetworkManager/conf.d/20-clone.conf` will enable stable mac address cloning (one different address for each network), and keep the vendor ID portion of the mac. NetworkManager has some great features.

```
systemctl enable --now NetworkManager
```

### @world
If you are using our `USE` settings and/or world config, you should update the world set as soon as you have a network connection:

    emerge --ask --newuse --deep @world

This will probably take a while.

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

### Screen Rotation:
#### Installing iio-sensor-proxy
`iio-sensor-proxy` handles accelerometers and light sensors, and is used for screen rotation and automatic brightness control, if the system has the necessary sensors. Our kernel includes a lot of modules for sensors.

If you are using our all of our config files, and you have updated your `world` set, `iio-sensor-proxy` should already be installed. Check with `emerge -pv iio-sensor-proxy`. If it is, skip ahead to [using iio-sensor-proxy](#using-iio-sensor-proxy)

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

