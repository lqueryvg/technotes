### Firewalld
- Netfilter is in linux kernel.
- Netfilter is managed by either iptables or firewalld.
- In firewalld, Interfaces & Services are assigned to Zones.
- A service is a name assigned to collection of ports and protocols.
```
firewall-cmd    # command line
firewall-config    # GUI
firewall-cmd --get-zones
firewall-cmd --get-services
/etc/firewalld/services   # create own services in here
/usr/lib/firewalld/services   # overview of pre-defined services

firewall-cmd --get-default-zone
firewall-cmd --set-default-zone dmz
# Add service 
firewall-cmd --zone=home --add-service=my-service --permanent

# restart firewalld to make permanent changes current
firewall-cmd --list-all   # list default zone and active services
```
#### Logging dropped/rejected packets (temporarily for debugging)
```
# find the index number of the final REJECT rule
iptables -L INPUT --line-numbers

# insert log rule just before final REJECT rule
iptables -I INPUT {index} -j LOG --log-prefix {MyDropPrefix}

# delete the temporary log rule when done
iptables -D INPUT {index}
```
