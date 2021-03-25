# Linux

## Users

```
/etc/default/useradd    # file containing useradd default settings
/etc/skel               # dir containing default user files

passwd -l               # lock user (puts !! in shadow)
passwd -u               # unlock
passwd -E yyyy-mm-dd    # expire on date, puts days since epoch in shadow
echo secret | passwd --stdin {user}     # can script passwords
usermod -aG sales lisa  # add to sales group (not changing her primary group)
john ALL = NOPASSWD: /bin/su -      # sudo - john root on all, no pass
```

## ACLs
- need acl mount option (this is default in XFS)
- Tip: set standard permissions before setting ACLs, otherwise gets messy
```
setfacl -R -m g:sales:rx /data/account    # modify
setfacl -m d:g:sales:rx /data/account     # set default
```

## Terminfo
```
/usr/share/terminfo/x/xterm     # compile terminfo for xterm
infocmp                         # decompile current terminfo
```

## Hostname
```
 # Debian
 vi /etc/hostname
 vi /etc/hosts

 # RHEL <7
 hostname john.localdomain
 vi /etc/sysconfig/network

 # RHEL 7
 hostnamectl set-hostname fred.localdomain
 hostnamectl status          # view
```

## Bluetooth
```
john# hcitool dev
Devices:
        hci0    00:26:83:37:F0:40

john# hcitool scan
Scanning ...
        54:9B:12:84:BB:F6       GT-I9100
```

## Bash Completion
```
yum install bash-completion
complete            # view configured bash completions
complete -d ls      # now ls only shows completes with directories
complete -P '">' -S '<"' some-command   # added pref/suffix
export FIGNORE='.tmp:.o'    # ignore files with suffixes
```

## Misc
```
egrep pattern\|$                # show all text with colour highlights
egrep --color=always pattern\|$ # send colour to pipe
sdiff -s file1 file2            # show diffs side by side, -s suppresses common lines, i.e. shows only diffs

less +F         # tail -f mode; Ctrl-C/Shift-f to switch modes
sed -i          # edit file in place
sed 2q          # print 2 lines
sed 5p          # print 5th line

column -t -s ,  # tabulate csv data

rename part part0 part?.mp4     # rename part?.mp4 to part0?.mp4

watch -n 1 systemctl status httpd   # run command every second, displaying
                                    # output fullscreen
killall {name}      # kill processes by name, exact match
pgrep -af {patt}     # list processes matching a pattern
pkill -If {patt}        # kill process matching pattern, -f matches all args, -I interactive
pkill -If -9 {patt}     # specify signal

man man-pages   # about man sections
man 7 glob      # globbing

ps fax      # show tree
free -h     # show free memory

top     # 1 = shows cpus
        # r = renice (-20 to +19, default=0)
        #  positive nice mean altruistic
        # us   user
        # sy   sys (kernel)
        # ni   nice'd user space processes
        # id   idle
        # wa   io wait
        # hi   hw interrupts
        # si   sw interrupts
        # st   stolen by another vcpu (if this is a VM)
        # total should be 100 (+/- 0.1 for rounding errors)

nice -n 10 command args     # run a value
default priority = 20
curl -LO {url}    # -L follows redirection, -O shows progress
strace -o'|cat' -e stat,open some-command        # strace stat calls to stdout
strace -c ls   # summarise time spent in system calls
cmd1 <(cmd2)   # process substitution:
               # makes output of cmd2 look like a temp file to cmd1

date --date='06/12/2012 07:21:22' +%s     # convert to unix time
date -jf "%T %D" "14:46:00 1/1/18" +%s    # same, on Mac
```
## Ag - Silver Searcher

```
ag -g vim ~     # find files containing word "vim"
~/.agignore     # list of patterns to ignore; plain word is treated as literal
```

## cron & at
```
atd         # daemon used by at
/etc/crontab        # main config file don't use this, can be overwritten
cd /etc; ls -d cront*       # directory for daily, weekly, etc; no specific time
                            # works across shutdowns
cron.d          # more like normal cron files, USE THIS
crontab -e      # create non-root user crontab
atq             # list at jobs
/var/spool/at   # file for each at job
```

## Hardware

```
objdump -f file | grep ^architecture    # check if 32 or 64 bit
lsusb
lsusb -v
lspci [-v]              # list graphics card among other things
modprobe -r {module}    # unload a module (+ dependencies)
modprobe {module}       # manually load (+ dependencies)
modinfo                 # details on a module, inc. params
modinfo cdrom lockdoor=0    # set a param
/etc/modprobe.conf      # pre RHEL7
/lib/modprobe.d         # from rpms, not typical place to make changes
man 5 modprobe
/etc/modprobe.d         # make mods here
vi cdrom.conf
options cdrom lockdoor=0
/sys/module
# and reboot
dmesg -w        # wait (like tail -f)
udevadm     # monitor udev
```

## Disk
```
fdisk -l        # show all disks & partitions
lsblk           # new and better
cat /proc/partitions
partprobe  # refresh partitions after fdisk
mount -t ext4 /dev/sdb1 /mnt        # mount second disk

ntfs-3g /dev/sdb1 /mnt      # mount usb, need fuse installed
mount -t ntfs-3g /dev/sda2 /mnt

iomega external usb
mount -t exfat /dev/sdb1 /mnt

du -BG -c *     # gigabyte units (-BG) with grand total (-c)
df -T           # show filesystem types

mount -o loop blah.iso /mnt     # mount an iso
```

### Multipath
```
multipath -k"list paths"
multipath -ll               # show all current info
multipath -v2
service multipathd reload
multipathd -k   # interactive mode
```

To re-order / rename luns:
```
multipath -f lun0 # flush (delete) an individual map
multipath -F      # flush (delete) all unused maps
multipath -r      # reload maps
```

#### Examine MBR
```h
dd if=/dev/sda of=/tmp/mbr.bin bs=512 count=1
file /tmp/mbr.bin
```

### Migrate disk using LVM

```
vgcfgbackup -vf /var/filename vg
fdisk -l [dev]    # should show no partitions on a clean volume
pvcreate [dev...]
vgextend vg [dev...]
pvmove dev1 dev2
vgreduce vg [dev...]
```
Then unzone old storage & cleanup reboot.

## Journal
Three places for logs to go:
1. direct (a file)
2. systemctl -> journald
3. rsyslogd classic approach (files in /var/log/)

### Forward journalctl info to rsyslog:
In /etc/rsyslog.conf:
```
$ModLoad imuxsock
$OmitLocalLogging off
```
In /etc/syslog.d/listend.conf:
```
$SYstemLogSocketName /run/systemd/journal/syslog
```
### Forward rsyslog to journalctl:
In /etc/rsyslog.conf:
```
$Modload omjournal
*.* :omjournal:
```

### logrotate
Runs as cron job.
```
/etc/logrotate.conf
/etc/logrotate.d        # files for additional settings
/var/log/secure
```

### encrypted volume
```
cryptsetup luksFormat /dev/sdb2
mkdir /secret
cryptsetup luksOpen /dev/sdb2 secret
mkfs.ext4 /dev/mapper/secret
mount /dev/mapper/secret /secret
umount /secret
cryptsetup luksClose /dev/mapper/secret
vi /etc/crypttab
secret /dev/sdb2  none      # prompt password during boot
```

### Tuning
```
/proc/cpuinfo
/proc/partitions
/proc/meminfo
/proc/sys/kernel
echo 0 > /proc/sys/net/ipv4/ip_forward
sysctl -a       # show params
/usr/lib/sysctl.d/blah.conf
/etc/sysctl.d/blah.conf
```

### httpd
```
/etc/httpd/conf/httpd.conf      # main conf file
```

### vsftpd
```
/etc/vsftpd/vsftpd.conf
/var/ftp    # doc root for ftp
```
### time
```
timedatectl list-timezones
timedatectl set-timezone Europe/London
timedatectl status
timedatectl set-time
timedatectl set-ntp yes   # starts crhonyd.service
hwclock --systohc       # set hardware clock to sys time
systemctl status crhonyd.service
/etc/chrony.conf
```
### mime
```
xdg-mime query filetype launch.ica

cd /usr/share/mime/packages
xdg-mime install --novendor Citrix-mime_types.xml

johnpc$ xdg-mime  query default application/x-ica
wfica.desktop       # good

johnpc$ xdg-mime query filetype launch.ica
text/plain      # bad

  # Note that these are shell scripts
  # xdg-mime will resort to using the file command (based on /etc/magic)
  # to determine file types if 1) you are not running under a desktop
  # environment and 2) the mimetype command is not installed.

```

### VNC

```
tigervnc          # client
tigervnc-server   # server
cd /usr/lib/systemd/system
cp vncserver@.service vncserver@\:1.service
vi vncserver@\:1.service    # change USER for a real vncuser
    # do not use session number 0
systemctl daemon-reload
su - vncuser
vncpasswd   # set a password as user, cannot be done as root
firewall-cmd --permanent --add-service vnc-server
firewall-cmd --reload
vncviewer -via vncuser@localhost localhost:1
```

## Ubuntu console keyboard mapping

```
cd /etc/default
vi console-setup
XKBMODEL="pc102"
XKBLAYOUT="gb"
setupcon  # must run this on console, not via ssh
# to fix at boot
update-rc.d -f console-setup remove
update-rc.d -f console-setup start 80 S .
# or /etc/sysconfig/keyboard file
```

## fstab

Example:

    #device    mountpoint type   options                               dump fsck-pass
    #host_home /host_home vboxsf noauto,uid=1000,gid=1000,dmode=777,fmode=777 0 1

