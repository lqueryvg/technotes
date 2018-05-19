## Shared Storage Pools (SSP)

VIO Command | Description
:-----------|:-----------
`cluster -list` | show cluster name (a good way to see if VIO is part of cluster)
`cluster -status -clustername mycluster -verbose` | more detail

- `ON_LEVEL` means pool at same level of VIO software.

- `UP_LEVEL` means software is higher than pool. Once all VIOs go
`UP_LEVEL` they will negotiate and pool will go up to new level,
with new features.

VIO Command | Description
:---------- | :----------
`lscluster -d` | Lots of info. Shows names of disks on all the VIO servers for storage pool. `CLUSDISK` is a cluster disk, i.e. a normal disk in the pool (0.25TB luns might be typical), `REPDISK` is a repository disk, which is how the VIOs talk to each other.|
`pv` | shows `pv` command usage
`pv -list` | shows disks.
`pv -list -capable` | shows what luns are available on all 
`failgrp -list` | Show mirror groups. `FG_NAME` of `Default` means that it's not mirrored.
`failgrp -create -fg myfgname: hdisk12 hdisk13` | Checks that luns are available on all VIOs, adds them to pool and starts mirroring.
`failgrp -modify -fg Default -attr fg_name=new_name` | rename fg
`failgrp -list` | 
`failgrp -list -verbose` |
`pv -list` | show which fgs the disks are in.
`pv -add -fg v7000tan: hdisk10 hdisk11 v7000dun: hdisk14 hdisk15` | Adds luns then starts spreading disk blocks around.
`pv -list -verbose` | shows more info, e.g.. `MPIO 2076 FC Disk`
`lsmap -all` | will show name of lu as backing device.
`lu -list` | shows lu names (typically use client lpar names)
`lu -create -lu orange4a -size 64G -vadapter vhost3` | creates a new lu as a backing device (very quick) and maps it to client (vhost).
`lu -create -lu orange4a -size 111G -vadapter vhost3` | creates another **with same name**. Beware.
`lu -remove -lu orange4a` | error because not unique.
`lu -remove -luudid blahdeblahdeblah` | need long id number if name not unique.

### Thin Provisioning

Default provisioning is thin. Can specify thick on `lu -create`
command line.

### Snapshots
Can stop an lpar, take a disk snapshot & then bring it back up.

### Repository Disk

Can use <1GB disk. Uses tiny part at beginning of disk.  VIOs only use
repo disk when changes are made so not used all the time..  One VIO at
a time is designated as the one which can write to the repo disk.

`chrepos` to move from one disk to another.

If the repository disk breaks, just use `chrepos`. It will see that
old disk is bad and write new stuff on the new disk.

If a VIO is down at the time, it will negotiate with other
VIOs and work out what went wrong. No need for intervention.

In any event, give it **time** to sort itself out.

SSP will continue to work without repo disk, but good to monitor
error logs and have a spare repo disk on hand just in case.

