# AIX

## HDLM

- `dlnkmgr view -drv` view dlm devices.
- `dlmrmdev` deletes dlm devices. Must be done before deleting underlying hdisk devices.
- `dlmpr -a`  clears all persistent reservations

Uninstall:
```sh
installp -u DLManager.mpio.rte
installp -u Hitachi.hacmp.support
installp -u Hitachi.aix.support.rte
```

## Powerpath

- `emcpreg -add some-key` to register powerpath software.
- `lsattr -El fscsixx`, may want to see `dynamic_tracking` and `fast fail` are on
- `lsattr -El device` check q depth (recommend 16) for all EMC or Powerpath devices seen in `lsdev -Cc disk`

### Network

- `no -p -o rfc1323=1` allows setting values greater than 4K; without this,
send/recv sizes can be set higher, but will only use first 4K.
- `tcpdump` puts interface into promiscuous mode 

## Misc Tips / Tricks
- `lssyscfg -r lpar -m msys --osrefresh` force refresh os information on HMC
- `lslock -e frame-name` list which HMC has a lock
- `viosvrcmd -m msys -p vio-name -c errlog` run command on vio from HMC
- `viosbr` dump VIOS config to a file. Use `-view` to view as a report.
- `print "cvai" | kdb | grep vscsi | grep -v read` show vio name and vhost
from client lpar
- LMB size must be same on all systems for LPM (use `lshwres` with `-F mem_region_size` to view)
- `NIMBUG` env variable - debug nim
- `alog -f file -o` to view alog format files (e.g. nimlog)
- `vmo -o nokilluid=uid_num` tell aix not to kill processes owned by this user
when out of memory
- `rmcdomainstatus -s ctrmc` test rmc connection, `I` = up, `O` = down.
- `pmcycles` show CPU speed

## Power8
- 12 cores per chip
- 8 SMT threads
- S824 up to 2TB
- CAPI adapters <500 instructions (vs 20K) to get to SSD disk
  - appears like slow memory rather than fast storage
  
### E870 / E880
- combine 795 & 770 & 780
- 16TB memory
- 19" rack
- mandatory midplane, with nodes (1 to 4) above and below
- no disk (SAS) in nodes; need IO drawer
- 32 cores per node; 4 node has 128 cores

## sugroups
If user A has sugroups = x,y,z
then all users in groups x,y,z can su to user A

## Shared Ethernet Adapters

Force a primary SEA (currently in auto mode) to fail over to standby...
```
chdev -l <SEA device> -a ha_mode=standby
```

Bring it back...
```
chdev -dev <SEA device> -attr ha_mode=auto
```

