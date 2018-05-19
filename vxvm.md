# VxVM

```sh
vxdisk -o alldgs list             # show all disks in cluster
vxdisk path                       # show OS, DA and DM names
vxddladm list                     # show which OS devs on which HBA

export PATH=$PATH:/etc/vx/bin     # for vxdisksetup & some other commands
export PATH=$PATH:/usr/lib/vxvm/bin:/etc/vx/bin:/opt/VRTSvcs/bin:/opt/VRTS/bin   # include everything
export MANPATH=$MANPATH:/opt/VRTS/man

vxconfigd       # just for config, doesn't affect data access
vxdctl init     # creates volboot file
vxdctl list
vxdctl enable
vxdctl get      # get debug state
vxlicrep        # display license info

vxdmpadm list dmpnode                # view mapping between /dev/vdx & virtio0_n
vxdisksetup -i virtio0_1                # add disk
vxdisk list                             # list disks
vxdisk list virtio0_1                   # disk detail
vxdg init dg1 virtio0_1                 # create dg
vxdg -g dg1 adddisk virtio0_2           # add disk to dg
vxdg list                               # list dgs
vxdg list dg1                           # dg details
vxdg -g dg1 free                        # free space on disks in dg
vxassist -g dg1 make vol1 30m           # create vol
vxinfo -g dg1                           # show vols in dg
                                        # Startable volumes are NOT started
mkfs -t vxfs /dev/vx/rdsk/dg1/vol1      # create fs
mount -t vxfs /dev/vx/dsk/dg1/vol1 /mnt # mount it
vxdisk -g dg1 resize c0t1d0s4           # grow dg after growing a lun
vxlist fs                               # filesystems
vxvol start -g dg1 vol1                 # start a volume

rm /etc/vx/reconfig.d/state.d/install-db  # if vxconfigd boot start problem
```

## Enclosures
```
vxddladm get namingscheme         # show naming scheme (Enclosure or OS Native)
vxddladm set namingscheme=ebn     # set EBN (Enclosure Based Naming)
                                  # ... effective immediately
vxdmpadm listenclosure all        # list enclosures
vxdmpadm setattr enclosure \
  name1 name=name2              # rename enclosure - effective immediately
                                # access names change if using EBN
```

## vxprint

`vxprint` reports all components.

`DISABLED` under `KSTATE` heading means vol not started.

Record types are as follows:

- dm  disk media (disk)
- v   volume (lv)
- dg  disk group (vg)
- sd  subdisk
- pl  plex
- sv  subvolume

## Disk Migration
```sh
vxconfigbackup -l /var/tmp/vxbackup dgname      # creates a subdir for backup
vxdctl enable                                   # if disks not visible
vxdisk list
fdisk -l /dev/sdxxx                             # check disk clean
vsdisksetup -i sdxxx                            # install private region, sdxxx is DANAME
vxdg -g dgname adddisk dmname=sdxxx etc...  # add new disks to dg
                                            # where dmname follows a unique naming standard
                                            # e.g. ssid_lunid
vxassist -bg dgname -o iosize=500k mirror volname dmname...     # for each vol to migrate
vxtask -l list
vxplex -g dgname -o rm dis volname-01   # for each vol migrated (but don't assume its suffix is 01)
vxdg -g dgname rmdisk old_dmname        # for each old disk
```

## Disk Access Names vs Media Names

### Disk `Access` Name.

The name given to a LUN as soon as Veritas first sees it.

Also called:
* `DA Name`
* `DEVICE` (e.g. in vxprint output)

If you are not using enclosure based naming, this could look the same
as an OS device file, **but don't bank on it**.

### Disk `Media` Name

The name given to a LUN when you put it in a Veritas disk group.
A LUN outside a disk group has no DM name.

Also called:
* `DM Name`
* `DISK` (e.g. in vxprint output)

You can give a LUN a custom media name (recommended) when you add it to
a diskgroup. If you don't, Veritas will choose a name which
may look the same as OS device file but **is not the same thing** and could lead to confusion.

### Confusion

Many of the Veritas commands refer to a Disk Access Name as "DEVICE" and a Disk
Media Name as "DISK", and neither of these are the same thing as an operating
system device name (i.e. as found in /dev/). If on Linux you are not using
enclosure based naming in Veritas (i.e. you are using OS Native based names)
and you are not explicitly assigning media names to the disks in your disk
groups, then all three names might look exactly the same - but they are _not_.

E.g.

```
$ vxdisk path
SUBPATH  DANAME   DMNAME       GROUP    STATE
<cut>
sdc      sda      sdb          mydg     ENABLED
sdy      sda      sdb          mydg     ENABLED
sda      sdc      sdc1         mydg     ENABLED
sdw      sdc      sdc1         mydg     ENABLED
sdd      sdj      sdc          mydg     ENABLED
sdz      sdj      sdc          mydg     ENABLED
sde      sdk      sdd          mydg     ENABLED
sdaa     sdk      sdd          mydg     ENABLED
sdf      sdl      sde          mydg     ENABLED
sdab     sdl      sde          mydg     ENABLED
sdg      sdm      sdf          mydg     ENABLED
sdac     sdm      sdf          mydg     ENABLED
```

In the above, the name "sdc" could refer to 3 different LUNs depending on
whether it is used as an operating system device name (e.g. fdisk /dev/sdc) or
an access name or a media name, and you therefore have to be extremely careful
that you are working with the correct disk.

It is therefore recommended that you:
- use **enclosure based naming**
- assign meaningful and unique **media names** to disks in a disk group


