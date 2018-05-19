#### samba

[HOWTO]
(http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_:_Ch12_:_Samba_Security_and_Troubleshooting)

[Samba & LDAP]
(http://www.ibm.com/developerworks/linux/tutorials/l-ldapsamba/section4.html#ibm-pcon)

Example samba public dir.<br>
*Don’t forget to chmod /test so that public can write.*

```
vi /etc/samba/smb.conf

[test]
   comment = Test Dir
   locking = no
   path = /test
   guest ok = yes
   read only = no
   create mask = 0644
   directory mask = 0755
   security = user
   encrypt passwords = true
   map to guest = bad user
   guest account = nobody
   guest only = yes

# Might need this...
smbpasswd -a john
```

Troubleshooting
```
testparm -s    # check syntax of smb.conf
testparm -s filename

# ping both ways
telnet {server} 139
smbpasswd john
smbclient  -U john //{server}/mnt
smbclient  -L {server} -U john    # list

```
