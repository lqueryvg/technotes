ldap
```
admin password typically = admin
apt-get slapd
apt-get lpad-utils  # ldapsearch
o   # organisation
c   # country
ou  # organisational unit
dc  # container; domain component
dn  # distinguished name
cn  # common name

DNS analogy:        lisa.rhat.com
LDAP equivalent:    cn=lisa,dc=rhat,dc=com
base context    where to start looking

ldd /bin/login      # see if pam library
/etc/pam.d          # login file points to others
system-auth         # most important

/etc/nslcd.conf     # ldap config
rpms:
   authconfig
   authconfig-gtk      # gui
   nscd                # name services cache daemon
   nss-pam-ldapd       # local name res & auth & connects to server
   pam_ldap            # libraries

authconfig-gtk
authconfig-tui          # text UI

systemctl status nslcd
nsswitch.conf       # sss no longer used in rhel7
cd /etc/openldap

connect to ldap server server.rhatcertification.com
TLS cert from ftp://server.rhatcertification.com/pub/slapd.pem
samba //server.rhatcertification.com/data
```
