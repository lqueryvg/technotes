
## grub2

### Boot issues
FYI, kernel mounts root. Systemd starts after.

In GRUB use `e` to edit line then add `rw init=/bin/bash` to linux line.
```
mount -o remount,rw /           # remount root when read-only in rescue
systemd.unit = emergency.target     # boot option
rd.break            # kernel param, stops at end of initramfs
```

Reset password, use `rd.break` method, then:
```
rd.break enforcing=0            # on GRUB line
mount -o remount,rw /sysroot
chroot /sysroot
passwd exit exit        # system boots
restorecon -v /etc/shadow
setenforce 1
# reboot later
```

### Verbose boot
```sh
# Persistent changes:
vim /etc/default/grub       # remove rhgb from GRUB_CMDLINE_LINUX
or /etc/grub.d              # add files, don't change

cd /boot/grub2
mv grub.cfg grub.cfg.safe
grub2-mkconfig -o grub.cfg    # builds new grub.cfg and updates MBR
```

### Default menu entry
`GRUB_DEFAULT=saved` in `/etc/default/grub` means that the boot entry
to be used is stored in binary file `/boot/grub/grubenv`.
```sh
grub2-editenv list                  # display entry saved in grubenv
grub2-set-default "blah1>blah2"     # set default entry, use '>' for submenus
```
