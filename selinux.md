# selinux
```
getenforce
setenforce permissive       # to troubleshoot
ls -Z                       # contexts
ps -Z
netstat -Ztulpen
getsebool -a
man semanage
man semanage-fcontext
semanage fcontext -l        # list contexts
restorecon -R -v /etc/      # restore contexts for all files in /etc
sepolicy manpage -a         # generate selinux manpages
mandb                       # update man database
man -k selinux
chcon       # don't use ! because writes to inode and not policy, so
            # relabel will break all of this
semanage -a -t my_context_t "/dir(/.*)?"    # use this
setsebool ftp_home_dir on       # temporary
setsebool -P ftp_home_dir on    # persistent
setroubleshoot  # selinux troubleshooting
systemctl start auditd
grep AVC /var/log/audit/audit.log
less /var/log/messages
sealert -l {long-hex-string}
semanage port -l   #List all port defitions
semanage port -a -t http_port_t -p tcp 81 # Allow listen on tcp port 81
semanage port -a -t ssh_port_t -p tcp 8991  # Allow listen on tcp port 8991
```

