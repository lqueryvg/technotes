# VCS

## Resources

Resources types:
-  on-off
-  on-only (e.g. NFS)
-  persistent (e.g. NIC)

Examples: e.g. filesystem, ip address, disk group, network, application

## Service Groups

A Service Group is a collection of resources for a service.

### Types
- failover
- parallel
- hybrid (cross-site)

### Actions
- freeze    ; stop it from failing over
- switch    ; move it
- disable   ; prevent from coming online
- enable    ; allow to come online

Cluster service group: special

## Agents

Manage resources: online, offline, monitor, clean, info

Types of config:
- active/passive
- active active
- n to 1
- n + 1
- shared storage
- shared nothing
- global replication

Install on Centos 6.3

```
vi /etc/redhat-release
# Add "Santiago" to get llt, gab and fencing rpms to install

cd /etc/VRTSvcs/conf/config
vi main.cf

export PATH=$PATH:/opt/VRTSvcs/bin
/opt/VRTSvcs/bin/hastatus -sum
/opt/VRTSvcs/bin/hagui  # on john

cd /opt/VRTS/install/
cd /opt/VRTSvcs/bin

./hastart -onenode
# need a UID for the cluster
/opt/VRTSvcs/bin/uuidconfig.pl  -clus -configure server1
/opt/VRTSvcs/bin/uuidconfig.pl  -clus -display server1

# iptables fix
vi /etc/sysconfig/iptables
-A INPUT -p tcp -m state --state NEW -m tcp --dport 14141 -j ACCEPT 
-A INPUT -p tcp -m state --state NEW -m tcp --dport 14150 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 14149 -j ACCEPT
service iptables restart

hasys -state    # show state
hastart			    # start had on this node
hastop -all		  # stop had on all nodes
haconf -makerw  # make config writable

hauser -add john -priv Administrator
haconf -dump      # save configuration
hares -state      # show where resources are
hagrp -state      # show where service groups are, or...
hastatus -sum     # show node and group statuses
hastatus      # show state changes, like tail -f
hagrp -online john_sg1 -any # bring group online
hagrp -switch john_sg1 -to server1  # move sg
hares -clear test_fs1 -sys server1  # clear fault for res on server1
hagrp -clear john_sg1 -sys server1  # clear fault for sg on server1
```
