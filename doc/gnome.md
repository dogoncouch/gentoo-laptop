
## Installing Gnome
This should be done once installation is complete, after rebooting into the new system. For this phase, do whatever you did to get networking to work on the install medium.

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
emergse --ask -v gnome
```

4. Enable/start GDM:
```
systemctl enable --now gdm
```

