
## Gnome
For this phase, do whatever you did to get networking to work on the install medium.

1. Copy files from `desktop-install-phase/etc` into `/etc`:
```
cp -r desktop-install-phase/etc/* /etc
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

