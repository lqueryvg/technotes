### NFS with Kerberos

`klist -k` on NFS server must show:

```
Keytab name: FILE:/etc/krb5.keytab
KVNO Principal
---- --------------------------------------------------------------------------
      1 host/nfsserver.example.com@EXAMPLE.COM
      1 host/nfsserver.example.com@EXAMPLE.COM
      1 host/nfsserver.example.com@example.COM
      1 host/nfsserver.example.com@example.COM
      6 nfs/nfsserver.example.com@EXAMPLE.COM
      6 nfs/nfsserver.example.com@EXAMPLE.COM
      6 nfs/nfsserver.example.com@EXAMPLE.COM
      6 nfs/nfsserver.example.com@EXAMPLE.COM
```

`klist -k` on NFS client must show:

```
Keytab name: FILE:/etc/krb5.keytab
KVNO Principal
---- --------------------------------------------------------------------------
   1 host/nfsclient.example.com@EXAMPLE.COM
   1 host/nfsclient.example.com@EXAMPLE.COM
   1 host/nfsclient.example.com@EXAMPLE.COM
   1 host/nfsclient.example.com@EXAMPLE.COM
```

On Centos 7, NFS server & client need the following running:

server                           | client
---------------------------------| -------
                                 | nfsiod             
nfsd       (multiple of these)   |                     
rpcbind -w                       |
rpc.mountd                       |
rpc.idmapd                       |
rpc.statd --no-notify            |
gssproxy XOR rc.svcgssd          |             
                                 | rpc.gssd
rpciod                           | rpciod  

