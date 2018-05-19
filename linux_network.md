## Networking
```
ip addr help     
ip a            # short for ip addr show
ip route show
ip addr add dev ens33 10.0.0.10/24      # always give mask
ip route add 20.0.0.0/8 via 192.168.4.4
  # change GATEWAY0 in script to make this persistent
nmcli con down ens33; nmcli con up ens33    # refresh config & ssh survives !

dig     # DNS checking
  # NOERROR is good
  # NXDOMAIN no such domain, but check SOA to see
  # who is saying this

ip -s link      # show bytes

systemctl restart NetworkManager

nmap
netstat -tulpen     # show listening processes
ping -f     # flood (bandwidth test)

ifconfig        # obsolete after RHEL7 (does not show address added above)
dhclient eth0   # call DHCP for a nic (can modify resolv.conf and routes)
losetup -a      # show status of loopback devices

ping -I eth1 10.1.1.5           # ping over a specific interface

dhclient -r     # release dhcp address
dhclient        # get new address

vi /etc/sysconfig/network  # high level conf, e.g. NETWORKING=yes and HOSTNAME
cd /etc/sysconfig/network-scripts
vi ifcfg-eth0               # set ONBOOT to yes
IPADDR1         # first ip alias

service network restart
service network status
```

## Network Manager
```
service NetworkManager start        # set ONBOOT="no" for NM_CONTROLLED nics
nmcli dev status                    # show network manager status
nmcli con add con-name ens33 ifname ens33 type ethernet ipv4 10.0.0.13/24
nmtui       # text user interface
nmcli con add con-name testing ifname ens33 type ethernet ip4 10.0.0.15/24
  # creates new scripts too
nmcli conn down ens33
nmcli conn up testing
```

## IP Forwarding
```
sysctl net.ipv4.ip_forward      # show  ip forwarding
cat /proc/sys/net/ipv4/ip_forward
sysctl -A | grep ip_for
```
### temp change without reboot
```
sysctl -w net.ipv4.ip_forward=1
echo 1 > /proc/sys/net/ipv4/ip_forward
```
### persistent change
```
vi /etc/sysctl.conf
net.ipv4.ip_forward = 1
sysctl -p /etc/sysctl.conf
 # or…
service network restart
```

## Link Aggregation with teaming
```
man nmcli-examples | grep team    # show command format to create team and slaves
rpm -ql teamd                     # list example file locations

# create team
cp /usr/share/doc/teamd-1.9/example_configs/roundrobin.conf /tmp/temp.conf
vi /tmp/temp.conf   # change the ports line to used desired devices
# watch the console in another window
nmcli con add type team con-name team0 ifname team0 config /tmp/temp.conf
nmcli con add type team-slave con-name team0-ens4 ifname ens4 master team0
nmcli con add type team-slave con-name team0-ens5 ifname ens5 master team0

# set IP
man nmcli | grep addr     # show command format to set ip address
nmcli con mod team0 ipv4.addresses 192.168.4.220/24

teamdctl team0 state      # show child states
teamnl team0 ports        # show port speeds
```

