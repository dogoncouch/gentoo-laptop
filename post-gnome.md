
## Networking
Should just work via gnome settings once NetworkManager is enabled. `/etc/NetworkManager/conf.d/20-clone.conf` will enable stable mac address cloning (one different address for each network), and keep the vendor ID portion of the mac. NetworkManager has some pretty cool features.
```
systemctl enable --now NetworkManager
```

## Bluetooth:
```
rfkill unblock bluetooth
hciconfig bluetooth up
systemctl start bluetooth
```

To Do: Full bluetooth control via gnome

## Screen rotation:
```
systemctl start iio-sensor-proxy
```

Works after first suspend for some reason.
To Do: Create startup config and use systemctl enable.

