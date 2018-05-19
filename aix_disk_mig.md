## Disk Migration

### Migration Scenario 1

A fairly complicated scenario, with 2 node cluster mirrored to a remote site.
Scenario involves migration disks from one storage server type to another using
LVM mirroring. Remote mirroring splits and re-syncs can be inserted into these
procedures as appropriate.

Beforehand, `cfgmgr -vl fcsxx` for every fcs adapter, check new disks all
present and correct.

On all 4 nodes:
- stop the cluster (all nodes and resource groups)
- unmount filesystems and varyoff vgs

Nodes 1 & 2:
- remove devices & driver software
- install new driver software & `cfgmgr` new devices
- check new disk device settings as per standards/recommendations with `lsattr`

Node 2:
- `varyonvg vgname`
- `extendvg vgname new_disk...`
- `mirrorvg -s vgname`, (`-s` means don't start the sync yet)
- `syncvg -P32 -v vgname`, uses max parallel LPs
- `unmirrorvg vgname old_disk...`
- `reducevg vgname old_disk...`
- For heartbeat disks, there may be custom disk methods for different storage
  vendors, break reservations.
  See `cllscustomdisk` command to view/change these.

Node 1:
- `exportvg old_vgs`
- `chdev -l new_disk -a pv=yes`, to get new pvid on shared disks
- `importvg -V majornum -y vgname new_disk`, using major numbers from node 2 vgs

Node 2:
- add new heartbeat disks to HA config via smitty
- remove old heartbeat disks from HA config
- sync cluster

Nodes 1 & 2:
- use `dhb_read` to check hb disks communicating properly
- start cluster & carry out tests
- cleanup reboot if necessary

Nodes 3 & 4, once disk mirroring in sync (if it was split before the
maintenance started):
- Replicate the Nodes 1 & 2 steps to delete the old devices & driver software
  and install/configure the new ones.
- Replicate the Node 1 steps to import the vgs from the new disks, using the
  same major numbers.

Node 4:
- Replicate the steps to remove the old HB disks from the HA config and add the new ones.
- Sync the cluster.
- Start the cluster and carry out some testing.


### Migration Scenario 2

Scenario: client lpar with SAN disks presented via vscsi.
The SAN disks are cloned by storage server and the copies need to be
presented to the client instead of the originals.

- `bootlist -m normal -ov`
- note the boot device path, e.g. `/vdevice/v-scsi@30000002/disk@8400000000000000:2`
- shutdown client lpar
- delete mappings and disks from VIO
- clone disks
- configure new disks on VIO, check attributes
- map new disks to client lpar
- `lsmap -all | grep -p {boot_disk}`
- note the LUN location 0x8100000000000000
- boot lpar to open firmware prompt
- use the old path, substituting the long LUN number found in lsmap
- `boot /vdevice/v-scsi@30000002/disk@8100000000000000:2`
- check disk attrs on lpar
- check and fix bootlist in lpar:
  - `bootlist -o -m normal` and compare with before
  - `bootinfo -v` to get blv, e.g. `hd5`
  - `lslv -l hd5` to get blv hdisk
  - `bootlist -m normal {hdisk} blv=hd5`   # watch for multiple blvs
- remove defined disks

