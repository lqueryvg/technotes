#### ISCSI

Target (the storage server)
```
service tgtd force-stop             # force stop (sometimes useful)
tgt-admin -s                        # show targets and lun info

tgtadm --lld iscsi --tid 1 --mode logicalunit \
  --op new --lun 1 -b /tmp/iscsi-disk1              # map new lun
tgtadm --lld iscsi --tid 1 --mode logicalunit \
  --op delete --lun 7                               # delete lun
```

Initiator (aka client or host)
```
iscsiadm -k=0                        # immediate iscsid stop *careful*

# Interfaces
iscsiadm -m iface                               # show interfaces
iscsiadm -m iface -I iscsi-eth1 --op=delete     # delete iface
iscsiadm -m iface -I iscsi-eth2 --op=new        # add new iface
iscsiadm -m iface -I iscsi-eth2 --op=update \
  -n iface.hwaddress -v 08:00:27:75:49:07       # update iface MAC
iscsiadm -m iface -I iscsi-eth2 --op=update \
  -n iface.net_ifacename -v eth2                # or use dev name
cd /var/lib/iscsi/ifaces; cat *                 # interfaces details
```

Discovery
```
iscsiadm -m discovery -t st \          # asking tgtd to send list of targets
  -I iscsi-eth1 -p centos3a            # ... only using this iface
iscsiadm -m discovery -t st \
  -I iscsi-eth2 -p centos3b
iscsiadm -m node                       # show discovered nodes
iscsiadm -m node -P 1                  # tree with more detail

# Discovery DB
iscsiadm -m discoverydb -P1            # display discovery db nodes
iscsiadm -m discoverydb  -t st \
  -p centos3 -o delete                 # tidy entry from discovery db
```

Sessions
```
iscsiadm -m session                    # show all logged in sessions
iscsiadm -m node --loginall=all        # login all sessions
iscsiadm -m node -u                    # logout all sessions

iscsiadm -m session -R                 # rescan luns
iscsiadm -m session -P 3               # lots of tgt info, including lun devs
```
