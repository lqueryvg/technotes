# GPFS
General Parallel Filesystem

## GPFS daemon
- Runs on each node.

## NSD Server
- A component of the GPFS daemon
- NSD = Network Shared Disk
- A GPFS node providing access to a disk for another GPFS node.
- Can be up to 8 NSD servers for each NSD.
- If one NSD server fails, the next in the list is used.
- Each NSD server must have a physical path to the disk.
- `mmnsddiscover` manually triggers discovery of physical paths to disk.
- By default a client will use a local path to the disk before accessing via NSD.

## NSD Client
- A GPFS node accessing the disk from a GPFS server.

## GPFS cluster
- can contain a mix of operating systems
- cannot have different operating systems accessing the same SAN disk
- clusters can be connected so that GPFS filesystems are accessible via nodes in both clusters

## Cluster Manager Node
- one per cluster, run `mmlsmgr -c` to see which.
- detects node failures
- distribute config changes
- selects **Filesystem Manager** node
- handle uid mappings

## Filesystem Manager
- one per filesystem, use `mmlsmgr` to see which
- adds disks
- disk availability
- filesystem repair
- manage space allocation
- token management
- quota management

## Metanode
- one per open file
- usually the node with the file open for the longest time
- handles all metadata writes for the file

## Filesystem
- **File System Descriptor** is written to fixed place on each disk in stripe group
- can control which disks in group store metadata (inodes & indirect blocks) via `mmcrfs` & `mmchdisk`
- File System Descriptor which disks are part of the stripe group.
- recovery logs; assigned to each node that accesses the filesystem

## Network
- different networks can be specified for GPFS daemon comms via `mmaddnode`, `mmchnode` & `mmcrcluster`
- used by GPFS commands; can use a separate network for admin

## Commands
- `mmstartup` - start the daemon
- `mmmount`, `mmcheckquota` - filesystem commands
- `mmfsck` - has online and offline modes
- `mmchdisk` - after disk unavailability has been corrected
- `mmlsdisk` - show disk states (`up`, `down` or `recovering`)



