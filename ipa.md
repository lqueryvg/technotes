### Red Hat Identity Management  (IdM)
Based on FreeIPA project.
- 389 Directory Server
- Single Sign on
- Includes Certificate System (Dogtag Project)
- Includes NTP Server (not compatible with chrony)
- Includes optional DNS server
Install ipa-server & ipa-client, then run ipa-server-install.
```
ipa user-add john
ipa passwd john
ipa user-find john
ipa-blah        # press TAB after ipa- to give list of possible commands
authconfig      # 
```

#### Installation Preparation
- setup hostname resolution: server needs to be able to lookup it's own name and DNS name.
- disable nscd and any LDAP or kerberos services, and NTP and DNS bind
- open firewall ports for web, ldap kerberos, dns, ntp

Installation:
```
yum -y install ipa-server bind nds-ldap
ipa-server-install      # prompted install
systemctl restart sshd
ipa user-find admin     # should find 1 record
kinit admin             # obtain kerb credentials
klist                   # verify ticket
ipa user-add fred
ipa passwd fred
ipa help commands
ipa help {command}
https://server.example.com      # access IPA interface
```
authconfig-tui example:
- select Use LDAP, Use Shadow Passwords, Use Kerberos, Local auth sufficient
- select Use TLS, Server is `ldap://blah.example.com`
- Base DN `dc=example,dc=com`
- Realm: `EXAMPLE.COM`
- Use DNS to resolve hosts and locate KDCs for realms

`cd /etc/openldap/cacerts`, get cacert.p12 from server

If doesn't work, and you get cert problems:
```
vim /etc/nslcd.conf     # uncomment tls_reqcert never, for testing
# If using sssd.conf
vi /etc/sssd/sssd.conf      # add line ldap_tls_require_cert=never
sssd            # System Security Services Daemon
sss_cache -E    # refresh cache after changing HBA rules
```

Files:
```
/etc/sysconfig/authconfig
```
