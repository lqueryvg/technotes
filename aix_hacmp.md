## HACMP

View cluster state:
```sh
/usr/es/sbin/cluster/clstat -o

cd /usr/es/sbin/cluster/utilities
./clRGinfo
./cltopinfo -i   # heartbeat info
./cltopinfo -m   # check no heartbeats missed
```

To stop cluster:
- `smitty clstop`, select all nodes in cluster and `Bring Resource Groups Offline`.
- `tail -f /var/hacmp/log/hacmp.out`

## PowerHA 7.1
- cluster repository disk (1GB)
- stretched cluster would have 1 repo disk
- cluster survives if repo disk fails, but topo commands will failed until fixed
- can define a backup repo  disk, but is a manual backup
- CAA cluster always active
- `clshowsrv -v` shows processes; clcomd name has changed
- source ip means packet response from alias address is correct
- netstat -i shows service alias first
- `clcmd` distributes commands to cluster nodes
- linked cluster would have repo disk disk in each remote site

### HyperSwap
- in enterprise edition
- nodes need to see both storage servers

### split/merge
- after split site, one is prod
- on merge, who becomes prod ? An option is available to request manual intervention.

